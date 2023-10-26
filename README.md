# RabbitMQ Tinker
Tool Collection for rescuing your RabbitMQ Cluster from particular bugs.

# Retail Shake Usage

As root, clone this repo from the server where the `RabbitMQ` container runs.

``` sh
cd ~root
git clone git@github.com:Retail-Shake/rabbitmq-tinker.git
```

Copy `RabbitMQ` `wal` files to run files analysis

``` sh
cd /home/docker/rabbitmq/data/mnesia
for i in $(find | grep -i wal   | xargs ls); \
do; \
    d=$(dirname $i); \
    f=$(basename $i); \
    mkdir -p ~root/rabbitmq-tinker/workspace/$d ;\
    cp $i ~root/rabbitmq-tinker/workspace/$d;\
done

```

Run the analysis

``` sh
cd ~root/rabbitmq-tinker
for i in $(find workspace/ -type f);\
do \
    echo $i; \
    python3 ra_wal/ra_wal_tinker.py $i; \
    echo ====================; \
done
```


When a file is corrupted you see it in output's script which mention `1 corrupted entries`

```
workspace/rabbit@rs_rabbitmq/quorum/rabbit@rs_rabbitmq/00037248.wal
file magic[b'RAWA']
file version[1]
check sum failed, expect_checksum[4143057477] actual_checksum[3368169214] entry_len[159] pos[167690370].
checksum is zero, meet file tail[167690379], stop.
scan wal file, find 450892 normal entries, 1 corrupted entries. <<<< Here
```

To fix the corrupted file, run the script again with `--truncate` as last argument

``` sh
python3 ra_wal/ra_wal_tinker.py workspace/rabbit@rs_rabbitmq/quorum/rabbit@rs_rabbitmq/00037248.wal --truncate
```

Then copy back the fixed file into `/home/docker/data/mnesia/rabbit@rs_rabbitmq/quorum/rabbit@rs_rabbitmq/00037248.wal`

