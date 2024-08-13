# Kafka Group Coordinator Log Anaylze

协调器出现下列日志

![xxxx_1833a0a5-5ef9-4665-aecc-c4a3a8352a37](https://github.com/armorsuitable/armorsuitable.github.io/assets/9997624/7f7cfea1-c0a2-44e9-a5a5-98f08aacea95)


![xxxxx88ce7f6e-653f-4c50-b657-fa0f033c0dfa](https://github.com/armorsuitable/armorsuitable.github.io/assets/9997624/b1b463d5-56ee-4f26-9c87-825ff959af01)


对应消费coordinator的leader分区启动，可能是__consumer的leader发生了切换？

查看 controller 日志

![xxxx-33c3-4290-81c3-08457f8b1322](https://github.com/armorsuitable/armorsuitable.github.io/assets/9997624/a26e0fa6-543a-4b69-9027-612c23958a4b)

controller切换，可能会触发分区的重新选举

## 进一步验证

查看 zk的/brokers/topics/__conusmer .../partitions/17/state的更新时间， 有leader切换就会更新这个节点


验证确实发生了leader 切换
