#Fabric的BlockChain网络建立过程#
>网络由实体：ORDERER认证服务节点、PEER节点、 虚体：账本、证书、信道、合约、组织

None

1.ORDERER节点运行时为网络组建的起点，ORDERER节点读取特定的配置文件，该配置文件指定了网络的管理员组织R4，这也意味着ORDERER节点属于R4，到目前为止，R4拥有对网络的所有权，网络中有且仅有R4一个成员。R4通过认证服务CA4所分发的X.509证书来确认自己的身份不会被盗用

ORDERER(属于R4),R4,CA4

2.R4更新了配置规则，使得组织R1也获得管理权限，此时R4和R1拥有对网络同样的管理权限，此时CA1也作为网络的一部分被添加进来，C1用于对R1中的节点身份进行分发认证

ORDERER(属于R4),R4,CA4;R1,CA1

3.由于R1和R2基于共同的需求需要进行信息交流,R1在配置中新增了一个CONSORTIUM，该团体包括了R1和R2两个组织，此时CA2由于R2的加入，同样也加入到了网络中，R2由R1分配的有限的权限

ORDERER(属于R4),R4,CA4;[R1,CA1;R2,CA2]

4.团体X1创建信道C1，C1由X1的成员R1和R2共同管理，R1和R2可以通过C1进行通讯，但R4无法介入。C1有其独立的配置文件CC1，这和其他网络的配置文件完全独立

5.节点P1加入，节点中保存了当前账本的拷贝L1。这里的保存，意味着L1在物理上存储在P1上，而在逻辑上，可以认证L1保存在信道C1上。P1是属于R1的基础设置，R1需要在CC1中提前配置好P1的相关许可权限，这样当P1运行以后，将想O4通过C1发送请求，O4根据CC1的配置来决定P1的准入资格，而CC1决定了P1对L1的读写权限

6.外部的客户端应用A1属于R1，因此A1可以通过信道C1来和节点P1以及ORDERER O4进行通讯，A1同样有来自CA1的授权证书以准许其加入上述网络。A1实际上并不能直接通过C1访问到节点P1上的账本L1的数据，P1上需要安装合约S5，该合约由开发者规定了若干种对L1的操作。当S5开发完成后，将由R1中的管理员来创建一个ChainCode，该ChainCode将对S5打包后安装到指定的节点中，例如P1节点