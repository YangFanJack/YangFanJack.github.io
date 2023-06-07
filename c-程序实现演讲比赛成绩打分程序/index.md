# C++程序实现演讲比赛成绩打分程序


## 程序总体框架

### 1.speaker类：演讲选手类

```c++
class Speaker
{
public:
    string m_Name;    //严格意义上这俩数据成员应该是私有的，用set和get函数来调用
    double m_Sorce[2];//此处是为了简便，严格意义上这样是不规范大气的

    Speaker();
    ~Speaker();
};
```

### 2.speechManager类：演讲比赛类（主体）

```c++
class SpeechManager
{
public:
    SpeechManager();
    ~SpeechManager();
    void show_Menu();//打印菜单
    void exitSystem();//退出系统
    void initSpeech();//初始化speechManager类的各个数据成员
    void createSpeaker();//创建参加比赛的12名选手
    void startSpeech();//比赛的主函数，包含所有流程，所有分流程又拆分为单独函数
    void speechDraw();//抽签
    void speechContest();//比赛（重点）
    void showScore();//显示晋级选手
    void saveRecord();//保存每一届冠亚季军到csv文件
    void loadRecord();//加载文件中每一届数据到程序中
    void showRecord();//显示文件中的数据（每一届的冠亚季军）
    void clearRecord();//清除文件记录

    vector<int> v1;//保存每一届的最初12名选手
    vector<int> v2;//保存每一届第一轮比赛晋级的6名选手
    vector<int> vVictory;//保存每一届冠亚季军
    map<int,Speaker>m_Speaker;
    map<int,vector<string>> m_Record;//存放往届记录
    int m_index;//保存是每一届比赛的第几轮
    bool fileIsEmpty;//判断文件是否为空
};
```

### 3.main.cpp(主函数)

```c++
int main()
{
    int choice=0;
    while(true)
    {
        srand((unsigned int)time(NULL));
        SpeechManager sm;
        sm.show_Menu();
        cout<<"please give me a choice:"<<endl;
        cin >>choice;
        switch(choice)
        {
        case 1://开始演讲比赛
            sm.startSpeech();
            break;
        case 2://查看往届记录
            //sm.loadRecord();
            sm.showRecord();
            break;
        case 3://清空比赛记录
            sm.clearRecord();
            break;
        case 4://退出比赛程序
            sm.exitSystem();
            break;
        default:
            system("cls");
            break;
        }
    }
    return 0;    
}
```

## 比赛大致流程

> 每一届的比赛从12名选手中选取得到，12名选手分为6  6两组，然后分别从6中选3，第一轮比赛得到6名晋级选手。
>
> 第二轮比赛中同样是从晋级的6名选手中选3人，按照成绩评选为每一届比赛的最终冠军，亚军和季军。

### SpeechManager类中的函数（实现功能的重要函数）

```c++
SpeechManager::SpeechManager()
{
    this->initSpeech();
    this->createSpeaker();
    this->loadRecord();
}

SpeechManager::~SpeechManager()
{

}

void SpeechManager::show_Menu()
{
    cout<<"***************************************"<<endl;
    cout<<"****************Welcome!***************"<<endl;
    cout<<"************1.开始演讲比赛**************"<<endl;
    cout<<"************2.查看往届记录**************"<<endl;
    cout<<"************3.清空比赛记录**************"<<endl;
    cout<<"************4.退出比赛程序**************"<<endl;
    cout<<"***************************************"<<endl;
    cout<<endl;
}

void SpeechManager::exitSystem()
{
    cout<<"欢迎下次使用！"<<endl;
    system("pause");
    exit(0);
}

void SpeechManager::initSpeech()
{
    this->v1.clear();
    this->v2.clear();
    this->vVictory.clear();
    this->m_Speaker.clear();
    this->m_index=1;
    this->m_Record.clear();
}

void SpeechManager::createSpeaker()
{
    string nameSeed = "ABCDEFGHIJKL";
    for(int i = 0;i<nameSeed.size();i++)
    {
        string name="Speaker";
        name+=nameSeed[i];
        Speaker sp;
        sp.m_Name=name;
        for(int j=0;j<2;j++)
        {
            sp.m_Sorce[j]=0;
        }
        this->v1.push_back(i+10001);
        this->m_Speaker.insert(make_pair(i+10001,sp));
    }
}

void SpeechManager::startSpeech()
{
    //第一轮比赛
    //抽签
    this->speechDraw();
    //比赛
    this->speechContest();
    //显示晋级结果
    this->showScore();

    //第二轮比赛
    this->m_index++;
    //抽签
    this->speechDraw();
    //比赛
    this->speechContest();
    //显示晋级结果
    this->showScore();

    //保存分数
    this->saveRecord();
    cout<<"本届比赛完毕！"<<endl;
    system("pause");
    system("cls");
}

void SpeechManager::speechDraw()
{
    cout<<"第"<<this->m_index<<"轮比赛正在抽签。。。"<<endl;
    cout<<"___________________________________"<<endl;
    cout<<"抽签结果如下："<<endl;

    if(this->m_index==1)
    {
        random_shuffle(v1.begin(),v1.end());
        for(vector<int>::iterator it=v1.begin();it!=v1.end();it++)
        {
            cout<<*it<<" ";
        }
        cout<<endl;
    }
    else
    {
        random_shuffle(v2.begin(),v2.end());
        for(vector<int>::iterator it=v2.begin();it!=v2.end();it++)
        {
            cout<<*it<<" ";
        }
        cout<<endl;
    }
    cout<<"_________________________________"<<endl;
    system("pause");
    cout<<endl;
}

void SpeechManager::speechContest()
{
    cout<<"第"<<this->m_index<<"轮比赛正在比赛。。。"<<endl;
    multimap <double,int,greater<double>> groupScore; 
    int num=0;

    vector<int> v_Src;     
    if(this->m_index==1) 
    {
        v_Src=v1;
    }
    else
    {
        v_Src=v2;   
    }
    for(vector<int>::iterator it=v_Src.begin();it!=v_Src.end();it++)
    {
        num++;
        //评委打分
        deque<int> d;
        for(int i=0;i<10;i++)
        {
            double score=(rand()%401+600)/10.f;
            cout<<score<<" ";
            d.push_back(score);
        }
        cout<<endl;
        sort(d.begin(),d.end(),greater<double>());
        d.pop_back();
        d.pop_front();
        double sum=accumulate(d.begin(),d.end(),0);
        double avg=sum/(double)d.size();

        this->m_Speaker[*it].m_Sorce[this->m_index-1]=avg;
        groupScore.insert(make_pair(avg,*it));
        if(num%6==0)
        {
            cout<<"第"<<num/6<<"组名次："<<endl;
            for(multimap<double,int,greater<double>>::iterator it=groupScore.begin();it!=groupScore.end();it++)
            {
                cout<<"编号："<<it->second<<"  成绩："<<this->m_Speaker[it->second].m_Sorce[this->m_index-1]<<endl;
            } 
            int count=0;
            for(multimap<double,int,greater<double>>::iterator it=groupScore.begin();it!=groupScore.end()&&count<3;it++,count++)
            {
                if(this->m_index==1)
                {
                    v2.push_back((*it).second);
                }
                else
                {
                    vVictory.push_back((*it).second);
                }

            }
            groupScore.clear();  
        }
    }
    cout<<"第"<<this->m_index<<"轮比赛完毕。"<<endl;
}

void SpeechManager::showScore()
{
    cout<<"第"<<this->m_index<<"轮晋级选手如下："<<endl;
    vector<int> v;
    if(this->m_index==1)
    {
        v=v2;
    }
    else
    {
        v=vVictory;
    }
    for(vector<int>::iterator it=v.begin();it!=v.end();it++)
    {
        cout<<"选手编号:"<<*it<<" 姓名:"<<this->m_Speaker[*it].m_Name<<" 得分:"<<this->m_Speaker[*it].m_Sorce[this->m_index-1]<<endl;
    }
    cout<<endl;
    system("pause");
    system("cls");
    show_Menu();
}

void SpeechManager::saveRecord()
{
    ofstream ofs;
    ofs.open("speech.csv",ios::out|ios::app);
    for(vector<int>::iterator it=vVictory.begin();it!=vVictory.end();it++)
    {
        ofs<<*it<<","<<this->m_Speaker[*it].m_Sorce[1]<<",";
    }
    ofs<<endl;
    ofs.close();
    cout<<"文件记录已经保存！"<<endl;
    //更新文件不为空的状态
    this->fileIsEmpty=false;
}

void SpeechManager::loadRecord()
{
    ifstream ifs("speech.csv",ios::in);
    //文件不存在的情况
    if(!ifs.is_open())
    {
        this->fileIsEmpty=true;
        cout<<"文件不存在！"<<endl;
        ifs.close();
        return;
    }
    //文件空的情况
    char ch;
    ifs>>ch;
    if(ifs.eof())
    {
        cout<<"文件为空！"<<endl;
        this->fileIsEmpty=true;
        ifs.close();
        return;
    }
    this->fileIsEmpty=false;
    ifs.putback(ch);//放回上面读取的
    string data;
    int index=1;
    while(ifs>>data)
    {
        vector<string> v;//存放记录中的数据
        //cout<<data<<endl;
        int pos=-1;//查到逗号位置
        int start=0;
        while(true)
        {
            pos=data.find(",",start);
            if(pos==-1)
            {
                //没有找到
                break;
            }
            else
            {
                string temp = data.substr(start,pos-start);
                //cout<<temp<<endl;
                v.push_back(temp);
            }
            start=pos+1;
        }
        this->m_Record.insert(make_pair(index++,v));
    }
    ifs.close();
    /*for(map<int,vector<string>>::iterator it=m_Record.begin();it!=m_Record.end();it++)
    {
        cout<<"第"<<it->first<<"届: "<<"冠军编号:"<<it->second[0]<<"分数:"<<it->second[1]<<endl;
    }*/
}

void SpeechManager::showRecord()
{
    if(this->fileIsEmpty)
    {
        cout<<"文件为空或文件不存在！"<<endl;
    }
    else
    {
        for(int i=1;i<=this->m_Record.size();i++)
        {
            cout<<"第"<<i<<"届: "<<endl;
            cout<<"冠军编号:"<<this->m_Record[i][0]<<" 分数:"<<this->m_Record[i][1]<<endl;
            cout<<"亚军编号:"<<this->m_Record[i][2]<<" 分数:"<<this->m_Record[i][3]<<endl;
            cout<<"季军编号:"<<this->m_Record[i][4]<<" 分数:"<<this->m_Record[i][5]<<endl;
        }
    }
    system("pause");
    system("cls");
}

void SpeechManager::clearRecord()
{
    cout<<"是否确定清空数据文件：(y or n)"<<endl;
    string select;
    cin>>select;
    if(select=="y")
    {
        //确认清空
        ofstream ofs("speech.csv",ios::trunc);
        ofs.close();
        cout<<"清空成功！"<<endl;
    }
    else if(select=="n")
    {
        system("pause");
        system("cls");
    }
    else
    {
        cout<<"选项输入错误！"<<endl;
    }
    
}
```




