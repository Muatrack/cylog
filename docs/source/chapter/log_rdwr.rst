日志文件
========

概述
++++++++
    日志文件按需分类，每种类别使用不同的目录，同一目录下的日志类型相同，多个目录分别对应多种类别的日志。在管理方面，日志文件的创建采用预占的方式新建，且文件写满后不会删除文件。
    而是采用循环写的方式，按照文件名称的排序依次写数据。

    日志路径
        每种类别的日志使用相同的目录，且同一目录下只能存在此类别的日志文件，相同目录下的所有文件大小一致。日志目录的确定，在日志组建初始化时指定。

    日志文件大小
        分类日志所在的目录下可能存在多个日志文件，这些文件的大小一致，在日志初始化时通过预设的参数设定单文件的大小。

    日志文件的数量
        日志文件的多少，依赖设计中的预期，当需要写入的数据量较多时，调大文件数量，相反会降低文件数量。

    日志写满处理
        每一类日志，写数据的时候依据文件名称的排序依次写入，当某一文件\ *例如，名为 00*\ 写满后，日志组件将为此类的日志选中下一个用于写入的文件名为\ *01*\ ， 当目录下所有文件均被写满后， 
        则开始循环写，即向文件\ *00*\ 写入。

        写满判定：

            * 写数据过程，判断写数据偏移量 + 待写数据长度, 是否超出文件范围。写满后，选择下一个文件并更新文件头部信息。
            * 初始化时，选择文件但不做写满判断。

操作
++++++

初始化
------------
    初始化的目的在于遍历分类日志所在的目录中实际的存储状况，依据预设逻辑找出带写入的文件和读取此文件中写操作的偏移量，当新来消息需要写入时，直接向此文件的指定便宜处写入数据。
    初始化过程分为文件识别、文件定位

    识别
        即遍历目录，在指定的路径下遍历已存在的文件，对于每个文件读取其文件的总大小（单日志文件的预占大小）、读取文件的头部信息并解析当前文件的写偏移量、 读出文件的开始写入的日期。
        读取到的文件信息，以对象数组的形式返回给上层调用。

    定位
        #. 遍历目录，选择文件头部重写时间戳时间戳最大的文件。
        #. 获取上步选中文件中的可写入数据的偏移量。

写入逻辑
----------
    文件头
        读取文件当前写位置

            - 写位置为初始位置，将文件头部数据更新。
            - 写位置不为初始位置，不更新头部数据。

    文件内容
        需要写进文件的内容分为2部分： 文件头数据、一系列日志数据。

        * 文件头中包含文件的最大长度(即文件的预占大小)、文件结构的版本号、文件首次或者再次被重头开始写时的时间戳。 
        
        * 日志数据中包含日志数据的长度和日志数据, 表示长度的数据固定占用2个字节， 表示数据的部分长度不固定。

删除/覆盖
-------------
    为减少文件碎片，以覆盖的方式循环对文件进行写操作。

文件遍历
----------
    文件\ ``code here``\ 遍历

    .. code::
        
        // 入口函数这样写
        int main() {
            ...
        }

文件读取
---------


文件命名规则
++++++++++++
    目录
        xxx

    文件
        文件名称以00开始，下一个文件名称为01 ..., 如此当遍历文件信息后，可通过名称对文件进行排序，以保障文件的新旧有序。
