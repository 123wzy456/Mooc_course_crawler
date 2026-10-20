import requests  #发请求
import time   #处理返回的数据
import json   #控制时延
import random   #生产随机休眠时间
import os    #处理文件路径

SEARCH_PAGE="https://www.icourse163.org/search.htm?search=python#/"
#为了通过Referer校验

SEARCH_API="https://www.icourse163.org/web/j/mocSearchBean.searchCourse.rpc?csrfKey=b9727d01f93b406ebae518cf5071019c"
#返回json数据的API接口

headers={
    "accept": "application/json, text/plain, */*",
    "accept-encoding": "gzip, deflate, br, zstd",
    "accept-language": "zh-CN,zh;q=0.9,en;q=0.8,en-GB;q=0.7,en-US;q=0.6",   
    "cache-control": "no-cache",
    "content-length": "159",
    "content-type": "application/x-www-form-urlencoded;charset=UTF-8",
    "cookie":'EDUWEBDEVICE=e8b5172441e94ebb816c0ae93cd6d704; hb_MA-A976-948FFA05E931_source=cn.bing.com; WM_TID=MONUlxa3h1RBVFRRVBedFh6sRRzKIgW0; WM_NI=mhtFRyrWkr3fhGKpWez0gP%2B0Yf9RH1mdD3INeTnWf%2B17TSw7Xr7HgvMQ2xbhgzAajEFLgBAk3c59zgqtXUKR6F1wexKiaZLScvsmcGaPSjH9hKpyg%2FmGQ2CQfWZfmc0IYlM%3D; WM_NIKE=9ca17ae2e6ffcda170e2e6eeb1b14ab8afbcaacf61aab08fb3d15b869a9eacd94fab88bdb4d068a5ada598bc2af0fea7c3b92aafb8bca7d173b289a593b26eac998591d66fbaf0a9b1aa3c9bbaf7b3cb3bafbc8cd4cc46a78ca2b8e521f3e99cb4f63d9caef9a9e67e8f8ffe99e164a5a68f82e94f8694a9a3c461f1bdfbdaf96db3ac98b6ca5af69aff98e87489b7afbbcf59a1aabd8ee24da3909d8bcb33ac95c09ae625af999fd3c25ba6899687f069f497978dd037e2a3; NTESSTUDYSI=b9727d01f93b406ebae518cf5071019c; utm="eyJjIjoiIiwiY3QiOiIiLCJpIjoiIiwibSI6IiIsInMiOiIiLCJ0IjoiIn0=|aHR0cHM6Ly9jbi5iaW5nLmNvbS8="',
    "edu-script-token": "b9727d01f93b406ebae518cf5071019c",
    "origin": "https://www.icourse163.org",
    "pragma": "no-cache",
    "priority":"u=1, i",
    "sec-ch-ua":'"Microsoft Edge";v="155", "Chromium";v="155", "Not(A:Brand";v="24"',
    "sec-ch-ua-mobile":"?0",
    "sec-ch-ua-platform":"Windows",
    "sec-fetch-dest":"empty",
    "sec-fetch-mode":"cors",
    "sec-fetch-site":"same-origin",
    "User_Agent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/155.0.0.0 Safari/537.36",
}

#请求头,内容在请求头中寻找

PAGE_SIZE=100  #每页请求的课程数量
DELAY_TIME_1=0.4  #请求间隔时间
DELAY_TIME_2=0.2  #请求间隔时间
MAX_RETRY=3  #最大重试次数

#开始定义函数
#抓取当前页的函数
def page_fetch(page_num):
    #post请求的参数
    form_data={
        "mocCourseQueryVo":json.dumps({
            "keyword":"python","pageIndex":page_num,"highlight":"true","orderBy":0,"stats":30,"pageSize":20
        })
    }
    url_params={
        "csrfKey":"b9727d01f93b406ebae518cf5071019c"
    }

    #循环请求,直到成功或达到最大重试次数
    for i in range(MAX_RETRY):
        try:
            resp=requests.post(SEARCH_API,data=form_data,headers=headers,params=url_params)
            #返回的字符串转换为python字典
            print(resp)
            res_data=resp.json()
            #通过比较状态码判断是否返回成功
            if res_data.get("code")==0:
                print(f"第{page_num}页抓取成功")
                #抓取成功时的时延
                time.sleep(random.triangular(DELAY_TIME_1, DELAY_TIME_2))
                return res_data
            else:
                print(f"第{page_num}页抓取失败,code={res_data.get('code')},msg={res_data.get('message')}")
                #抓取失败时的时延
                time.sleep(random.triangular(DELAY_TIME_1, DELAY_TIME_2))
        except:
            print(f"第{page_num}页抓取异常,第{i+1}次抓取失败")
            #retry之间加时延
            time.sleep(random.triangular(DELAY_TIME_1, DELAY_TIME_2))
    return None

#主函数
def main():
    page=1
    raw_dir="pages"
    if not os.path.exists(raw_dir):
        os.makedirs(raw_dir)
    while True:
        raw_page_data=page_fetch(page)
        if raw_page_data is None:
            print(f"第{page}页抓取失败,跳过")
            page+=1
            continue
        file_path=os.path.join(raw_dir,f"page_{page}.json")
        #用with语句打开文件，自动关闭文件,w方式，没有则创建，覆盖原有内容
        with open(file_path,"w",encoding="utf-8") as f:
            #将抓取到的原始数据写入json文件中，ensure_ascii=False表示不转义非ASCII字符，indent=2表示缩进为2个空格
            json.dump(raw_page_data,f,ensure_ascii=False,indent=2)
        print(f"第{page}页数据已保存到{file_path}")
        course_list=raw_page_data.get("result",{}).get("list",[])
        if course_list==None:
            print(f"\n抓取完成,共抓取{page-1}页数据")
            break
        page+=1
#让程序只有在被运行时才执行main函数，而在被导入时不会执行
if __name__=="__main__":
    main()
