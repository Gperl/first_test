20180802


import os  #
import sys  #调试用包
import time  #计时包

def 制作标签字典(file_path):
    type_dict = {"spam": "1", "ham": "0"}
    index_file = open(file_path)     #./date/full/index
    index_dict = {}
    try:
        for line in index_file:
            arr = line.split(" ")
            #[spam,../data/000/000]
            if len(arr) == 2:
                key, value = arr
                #(spam),(../data/000/000)
            # 添加到字段中
            value = value.replace("../data", "").replace("\n", "")
            #(spam),(/000/000)
            index_dict[value] = type_dict[key.lower()]
            #{/000/000: 1}
            #{/000/001: 0}
    finally:
        index_file.close()
    return index_dict
# for file_path in path_list:
# 邮件的文件内容数据读取
def 字典化邮件文本内容(file_path):#dll
    './data/data/000/000'
    file = open(file_path, "r", encoding="gb2312", errors='ignore')
    content_dict = {}
    try:
        is_content = False#初始化为False后
        for line in file:
            #切掉两头的空格，逐步逼近格式化数据
            line = line.strip()
            if line.startswith("From:"):
                #From: "yan"<(8月27-28,上海)培训课程>,
                content_dict['from'] = line[5:]
                # "yan"<(8月27-28,上海)培训课程>#
                #{'from':"yan"<(8月27-28,上海)培训课程>}
            elif line.startswith("To:"):
                content_dict['to'] = line[3:]
                #lu@ccert.edu.cn
            elif line.startswith("Date:"):
                content_dict['date'] = line[5:]
                # Tue, 30 Aug 2005 10:08:15 +0800
            elif not line:
                is_content = True

            # 处理邮件内容,利用is_content的真假来执行一个小的针对文本内容的for loop
            if is_content:
                if 'content' in content_dict:
                    content_dict['content'] += line
                else:
                    content_dict['content'] = line
    finally:
        file.close()
        '释放栈中的内存地址'
    return content_dict

# 邮件数据处理
def 字典转文本(file_path):
    #先把内容读成字典
    content_dict = 字典化邮件文本内容(file_path)#先把非格式化邮件转化为字典

    # 再把字典转成文本
    result_str = content_dict.get('from', 'unkown').replace(',', '').strip() + ","
    result_str += content_dict.get('to', 'unknown').replace(',', '').strip() + ","
    result_str += content_dict.get('date', 'unknown').replace(',', '').strip() + ","
    result_str += content_dict.get('content', 'unknown').replace(',', ' ').strip()
    return result_str
    
#使用函数开始数据处理
start = time.time() #开始时间标记
index_dict = 制作标签字典('./data/full/index')

# index_dict = 制作标签字典('C:\\Users/Administrator/Desktop/index')#('./data/full/index')
# print(index_dict)
# sys.exit(0)
list0 = os.listdir('./data/data')   #文件夹的名称
#{000,001,002,215}
 
for l1 in list0:     #开始把N个文件夹中的file写入N*n个wiriter
    '循环文件夹'
    l1_path = './data/data/' + l1   #000
    #l1_path='./data/data/000',是文件夹的名称
    print('开始处理文件夹' + l1_path)#开始处理第000文件夹
    list1 = os.listdir(l1_path)#获取000文件夹内的所有文件名列表
    #[000,001]
    #list1文件列表
     
    write_file_path = './data/process01_' + l1
    #./data/process01_000
    #./data/process01_001
    #总共有216个
     #保存每个文件夹下面文件的文件 300行
    with open(write_file_path, "w", encoding= 'utf-8') as writer:
        for l2 in list1:
            '循环文件'
            l2_path = l1_path + "/" + l2#得到要处理文件的具体路径
             
            index_key = "/" + l1 + "/" + l2
             
            if index_key in index_dict:
                #{/000/000: 1}
                #{/000/001: 0}
                content_str = 字典转文本(l2_path)
                content_str += "," + index_dict[index_key] + "\n"
                writer.writelines(content_str)          
with open('./data/result_process01',"w", encoding ='utf-8') as writer:
    for l1 in list0:
        file_path= './data/process01_' + l1
        print("开始合并文件：" + file_path)
            
        with open(file_path, encoding = 'utf-8') as file:
            for line in file:
                writer.writelines(line)
#两个for嵌套共执行：  216*300=6W+        
            
end = time.time()
print('数据处理总共耗时%.2f'%(end- start))  
