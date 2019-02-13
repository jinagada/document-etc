# 목차
- [Elasticsearch Repository 설정 및 적용](#user-content-elasticsearch-repository-설정-및-적용)
  - [NFS 혹은 S3 디렉토리 마운트](#user-content-nfs-혹은-s3-디렉토리-마운트)
  - [Node 서버 설정](#user-content-node-서버-설정)
  - [Node 서버 재기동](#user-content-node-서버-재기동)
- [Repository 관리](#user-content-repository-관리)
  - [Repository 생성](#user-content-repository-생성)
  - [Snapshot 생성](#user-content-snapshot-생성)
  - [Snapshot 목록보기](#user-content-snapshot-목록보기)
  - [Snapshot 삭제](#user-content-snapshot-삭제)
  - [Snapshot 복원](#user-content-snapshot-복원)
  - [Snapshot 상태 확인](#user-content-snapshot-상태-확인)
  - [Repository 삭제](#user-content-repository-삭제)
  
# Elasticsearch Repository 설정 및 적용

## NFS 혹은 S3 디렉토리 마운트

- 공유 디렉토리(NFS, S3 등)를 아래의 위치에 마운트 할 것!
- 해당 디렉토리는 yourid 계정으로 접근이 가능해야함
- 현재의 마운트 디렉토리 위치 : /home/yourid/s3

## Node 서버 설정
- 모든 Node 서버에 동일하게 설정
```shell
# elasticsearch.ym 파일 수정
~$ vi local/elasticsearch/config/elasticsearch.yml
-- 내용 수정 -----------------------------------------------------------------------------------
내용생략....
# ----------------------------------- Paths ------------------------------------
#
# Path to directory where to store the data (separate multiple locations by comma):
#
#path.data: /path/to/data
#
# Path to log files:
#
#path.logs: /path/to/logs
# Add Shared File System Repository <== 추가
path.repo: ["/home/yourid/s3"] <== 추가
내용생략....
-- 내용 수정 -----------------------------------------------------------------------------------
```

## Node 서버 재기동
- 모든 Node 서버에 동일하게 진행

- Shard Allocation 중지 : 노드를 중단했을때 샤드들이 재배치 되지 않도록 다음 명령을 실행합니다.
- Kibana -> Dev Tools 에서 실행
```shell
PUT _cluster/settings
{
  "persistent": {
    "cluster.routing.allocation.enable": "none"
  }
}
```

- Sync Flus 실행 : Primary - Replica 샤드들 간의 세그먼트 저장 상태를 동기화 시켜줍니다.
- Kibana -> Dev Tools 에서 실행
```shell
POST _flush/synced
```

- Node 서버 재실행
```shell
# 디렉토리 이동
~$ cd ~/local/elasticsearch

# 서버 중지
elasticsearch$ ./stop.sh

# 서버 실행
elasticsearch$ ./start.sh
```

- Shard Allocation 재가동 : unassigned 된 샤들이 새 노드에 다시 배치되도록 다음 명령을 실행합니다.
- Kibana -> Dev Tools 에서 실행
```shell
PUT _cluster/settings
{
  "persistent": {
    "cluster.routing.allocation.enable": null
  }
}
```

- 노드 상태를 확인하며 대기
- 상태가 GREEN으로 되면 다음 서버 작업 진행

# Repository 관리

## Repository 생성

- Kibana -> Dev Tools 에서 실행
```shell
PUT /_snapshot/my_fs_backup <== Repository 명을 입력
{
    "type": "fs", <== Repository에서 File System을 사용
    "settings": {
        "location": "my_fs_backup_location", <== Repository 위치 설정
        "compress": true <== 메타 데이터 압축 여부(기본값 : true)
    }
}
예)
PUT /_snapshot/local_fs_backup
{
    "type": "fs",
    "settings": {
        "location": "sample_index_snapshot"
    }
}
==> 공유폴더 위치에 “sample_index_snapshot” 디렉토리가 생성됨
==> /home/yourid/s3/sample_index_snapshot
```

## Snapshot 생성

wait_for_completion=true 옵션은 시간이 너무 오래걸리는 경우 Kibana에 오류가 발생 할 수 있으므로 주의 해서 사용 할 것!

- Kibana -> Dev Tools 에서 실행
```shell
PUT /_snapshot/my_backup/snapshot_2?wait_for_completion=true <== Snapshot 명을 입력, wait_for_completion : 요청에 대한 응답을 바로 받을지(false : 기본값), 완료 후 받을지(true) 설정
{
  "indices": "index_1,index_2", <== Snapshot 생성 할 Index 명
  "ignore_unavailable": true, <== true 인 경우 설정한 Index 가 없으면 해당 Index에 대한 작업을 하지 않음
  "include_global_state": false <== Cluster 의 Global 상태가 포함 되지 않도록 false 로 설정
}
예)
PUT /_snapshot/local_fs_backup/snapshot_1?wait_for_completion=true
{
  "indices": "bigginsight",
  "ignore_unavailable": true,
  "include_global_state": false
}
==> /home/yourid/s3/sample_index_snapshot 디렉토리 하위에 아래와 같이 보관됨
-- 출력 내용 -----------------------------------------------------------------------------------
합계 7
drwxrwxrwx. 1 root root 4096  1월  2 15:05 .
drwxrwxrwx. 1 root root    0  1월  2 14:20 ..
-rwxrwxrwx. 1 root root   29  1월  2 14:56 incompatible-snapshots
-rwxrwxrwx. 1 root root  265  1월  2 14:58 index-1
-rwxrwxrwx. 1 root root  176  1월  2 15:05 index-2
-rwxrwxrwx. 1 root root    8  1월  2 15:05 index.latest
drwxrwxrwx. 1 root root    0  1월  2 14:56 indices
-rwxrwxrwx. 1 root root   90  1월  2 14:56 meta-vrn_S1JXS5Wv-diLztFXgQ.dat
-rwxrwxrwx. 1 root root  248  1월  2 14:56 snap-vrn_S1JXS5Wv-diLztFXgQ.dat
-- 출력 내용 -----------------------------------------------------------------------------------
```

## Snapshot 목록보기

- Kibana -> Dev Tools 에서 실행
```shell
GET /_snapshot/local_fs_backup/_all
```

## Snapshot 삭제

- Kibana -> Dev Tools 에서 실행
```shell
DELETE /_snapshot/local_fs_backup/snapshot_2
```

## Snapshot 복원

- Kibana -> Dev Tools 에서 실행
```shell
POST /_snapshot/my_backup/snapshot_1/_restore <== Repository 명/Snapshot 명/_restore 형태로 사용
{
  "indices": "index_1,index_2", <== 여러 Index 명을 사용 할 수 있음
  "ignore_unavailable": true, <== true 인 경우 설정한 Index 가 없으면 해당 Index에 대한 작업을 하지 않음
  "include_global_state": true, <== Snapshot 저장 시 Cluster 의 Global 상태를 저장한 경우(true) true 로 설정하여 restore 할 것!
  "rename_pattern": "index_(.+)", <== rename 할 Index 명 패턴 설정
  "rename_replacement": "restored_index_$1" <== 신규 Index 명 설정. rename_pattern 에서 “()”에 포함된 내용이 “$1” 에 매핑됨
}
예)
POST /_snapshot/local_fs_backup/snapshot_1/_restore
{
  "indices": "bigginsight",
  "ignore_unavailable": true,
  "include_global_state": false,
  "rename_pattern": "bigginsight",
  "rename_replacement": "restored_bigginsight"
}
```

## Snapshot 상태 확인

- Kibana -> Dev Tools 에서 실행
```shell
GET /_snapshot/local_fs_backup/snapshot_1/_status
```

## Repository 삭제

설정 정보에서는 지워지지만, 실제 파일(디렉토리 포함)은 지워지지 않고 그대로 남아있음
동일한 정보로 다시 생성 할 경우 이미 저장된 Snapshot 을 그대로 사용 할 수 있음

- Kibana -> Dev Tools 에서 실행
```shell
DELETE /_snapshot/local_fs_backup
```

# 참고 자료

* https://www.elastic.co/guide/en/elasticsearch/reference/6.5/modules-snapshots.html

