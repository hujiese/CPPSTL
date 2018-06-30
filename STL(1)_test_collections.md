## 1. 认识headers、版本、重要资源 ##
![](https://i.imgur.com/L0KlALx.png)
![](https://i.imgur.com/QA84282.png)
![](https://i.imgur.com/LgPB8Na.png)
![](https://i.imgur.com/W178PkA.png)
![](https://i.imgur.com/z1pQwVt.png)
![](https://i.imgur.com/tNqQYcQ.png)
![](https://i.imgur.com/PuBHbA1.png)

## 2. STL体系结构基础介绍 ##
![](https://i.imgur.com/t56bNs6.png)
![](https://i.imgur.com/jgBybaa.png)
![](https://i.imgur.com/IVPaSaz.png)
![](https://i.imgur.com/ADWlUFL.png)
![](https://i.imgur.com/tV48diS.png)
![](https://i.imgur.com/aeKUqEL.png)

## 3. 容器之分类与各种测试 ##
![](https://i.imgur.com/bkGJIVy.png)

### （1）测试array(VS2013) ###
	#define _CRT_SECURE_NO_WARNINGS
	#include <array>
	#include <iostream>
	#include <ctime> 
	#include <cstdlib> //qsort, bsearch, NULL
	using namespace std;
	
	const long ASIZE = 50000L;
	
	//用户输入需要查找的long类型元素
	long get_a_target_long()
	{
		long target = 0;
	
		cout << "target (0~" << RAND_MAX << "): ";
		cin >> target;
		return target;
	}
	
	//比较传入参数大小，a > b则返回1，否则返回-1
	int compareLongs(const void* a, const void* b)
	{
		return (*(long*)a - *(long*)b);
	}
	
	
	namespace jj01
	{
		void test_array()
		{
			cout << "\ntest_array().......... \n";
	
			array<long, ASIZE> c;//50000个long类型元素
	
			clock_t timeStart = clock();//开始计时
			for (long i = 0; i< ASIZE; ++i) {//为array随机生成50000个long类型元素
				c[i] = rand();
			}
			cout << "milli-seconds : " << (clock() - timeStart) << endl;	//打印初始化array时间
			cout << "array.size()= " << c.size() << endl;//打印数组长度
			cout << "array.front()= " << c.front() << endl;//打印数组首元素
			cout << "array.back()= " << c.back() << endl;//打印数组最后元素
			cout << "array.data()= " << c.data() << endl;//打印数组在内存的首地址
	
			long target = get_a_target_long();
	
			timeStart = clock();//排序查找开始计时
			::qsort(c.data(), ASIZE, sizeof(long), compareLongs);//排序
			long* pItem = (long*)::bsearch(&target, (c.data()), ASIZE, sizeof(long), compareLongs);//二分法查找
			cout << "qsort()+bsearch(), milli-seconds : " << (clock() - timeStart) << endl;	// 打印排序查找时间
			if (pItem != NULL)//判断是否找到
				cout << "found, " << *pItem << endl;
			else
				cout << "not found! " << endl;
		}
	}
	
	int main(void)
	{
		jj01::test_array();
		return 0;
	}

运行结果如下：

![](https://i.imgur.com/uDPrc8j.png)

### (2)测试vector(VS2013) ###

	#define _CRT_SECURE_NO_WARNINGS
	#include <vector>
	#include <stdexcept>
	#include <string>
	#include <cstdlib> //abort()
	#include <cstdio>  //snprintf()
	#include <iostream>
	#include <ctime> 
	#include <algorithm> 	//sort()
	using namespace std;
	
	const long ASIZE = 50000L;//随机生成字符数个数
	
	//用户输入需要查找的string类型元素
	string get_a_target_string()
	{
		long target = 0;
		char buf[10];
	
		cout << "target (0~" << RAND_MAX << "): ";
		cin >> target;
		sprintf(buf, "%d", target);
		return string(buf);
	}
	
	//字符串比较
	int compareStrings(const void* a, const void* b)
	{
		if (*(string*)a > *(string*)b)
			return 1;
		else if (*(string*)a < *(string*)b)
			return -1;
		else
			return 0;
	}
	
	namespace jj02
	{
		void test_vector(const long& value)
		{
			cout << "\ntest_vector().......... \n";
	
			vector<string> c;//定义一个vector容器存放string类型数据
			char buf[10];
	
			clock_t timeStart = clock();//开始计时
			//将value个string送入vector中
			for (long i = 0; i< value; ++i)
			{
				try {//这里使用try...catch是为了避免value太大导致内存不够出现异常
					sprintf(buf, "%d", rand());
					c.push_back(string(buf));//将string送入vector中
				}
				catch (exception& p) {
					cout << "i=" << i << " " << p.what() << endl;
					//曾經最高 i=58389486 then std::bad_alloc
					abort();
				}
			}
			cout << "milli-seconds : " << (clock() - timeStart) << endl;//打印初始化array时间
			cout << "vector.max_size()= " << c.max_size() << endl;	//1073747823
			cout << "vector.size()= " << c.size() << endl; // 打印vector长度
			cout << "vector.front()= " << c.front() << endl;//打印vector首元素
			cout << "vector.back()= " << c.back() << endl;//打印vector最后元素
			cout << "vector.data()= " << c.data() << endl;//打印vector在内存的首地址
			cout << "vector.capacity()= " << c.capacity() << endl << endl;//打印vector容量
	
	
			string target = get_a_target_string();//获取用户输入字符串
			//该代码块测试stl的find函数，查找用户输入字符串，并打印查找所花费时间
			{
				timeStart = clock();
				auto pItem = find(c.begin(), c.end(), target);
				cout << "std::find(), milli-seconds : " << (clock() - timeStart) << endl;
	
				if (pItem != c.end())
					cout << "found, " << *pItem << endl << endl;
				else
					cout << "not found! " << endl << endl;
			}
			//该代码块测试c标准库的二分查找bsearch函数，查找用户输入字符串，并打印查找所花费时间
			{
				timeStart = clock();
				sort(c.begin(), c.end());//排序
				cout << "sort(), milli-seconds : " << (clock() - timeStart) << endl;//打印排序时间
	
				timeStart = clock();
				string* pItem = (string*)::bsearch(&target, (c.data()),//二分查找
					c.size(), sizeof(string), compareStrings);
				cout << "bsearch(), milli-seconds : " << (clock() - timeStart) << endl;//打印二分查找时间
	
				if (pItem != NULL)
					cout << "found, " << *pItem << endl << endl;
				else
					cout << "not found! " << endl << endl;
			}
	
		}
	}
	
	int main(void)
	{
		jj02::test_vector(ASIZE);
		return 0;
	}

编译运行结果如下：

![](https://i.imgur.com/ZpwxUW2.png)

值得注意的是二分法查找总时间比stl内置的顺序查找方法还慢，这里主要想说明二分查找虽然快，但二分查找先前的排序耗时较多，所以不是万能的。

### (3)测试list(VS2013) ###
	#define _CRT_SECURE_NO_WARNINGS
	#include <list>
	#include <stdexcept>
	#include <string>
	#include <cstdlib> //abort()
	#include <cstdio>  //snprintf()
	#include <algorithm> //find()
	#include <iostream>
	#include <ctime> 
	using namespace std;
	
	namespace jj03
	{
		const long ASIZE = 50000L;//随机生成字符数个数
	
		string get_a_target_string()
		{
			long target = 0;
			char buf[10];
	
			cout << "target (0~" << RAND_MAX << "): ";
			cin >> target;
			sprintf(buf, "%d", target);
			return string(buf);
		}
	
		void test_list(const long& value)
		{
			cout << "\ntest_list().......... \n";
	
			list<string> c;
			char buf[10];
	
			clock_t timeStart = clock();
			for (long i = 0; i< value; ++i)
			{
				try {
					sprintf(buf, "%d", rand());
					c.push_back(string(buf));
				}
				catch (exception& p) {
					cout << "i=" << i << " " << p.what() << endl;
					abort();
				}
			}
			cout << "milli-seconds : " << (clock() - timeStart) << endl;
			cout << "list.size()= " << c.size() << endl;
			cout << "list.max_size()= " << c.max_size() << endl;
			cout << "list.front()= " << c.front() << endl;
			cout << "list.back()= " << c.back() << endl;
	
			string target = get_a_target_string();
			timeStart = clock();
			auto pItem = find(c.begin(), c.end(), target);//调用stl标准库算法find()
			cout << "std::find(), milli-seconds : " << (clock() - timeStart) << endl;
	
			if (pItem != c.end())
				cout << "found, " << *pItem << endl;
			else
				cout << "not found! " << endl;
	
			timeStart = clock();
			c.sort();//调用list内置sort()算法
			cout << "c.sort(), milli-seconds : " << (clock() - timeStart) << endl;
	
			c.clear();
		}
	}
	
	int main(void)
	{
		jj03::test_list(jj03::ASIZE);
		return 0;
	}

编译运行结果如下：

![](https://i.imgur.com/lEN6frm.png)

### (4)测试forward_list(VS2013) ###
	#define _CRT_SECURE_NO_WARNINGS
	#include <forward_list>
	#include <stdexcept>
	#include <string>
	#include <cstdlib> //abort()
	#include <cstdio>  //snprintf()
	#include <iostream>
	#include <ctime> 
	using namespace std;
	
	namespace jj04
	{
		const long ASIZE = 50000L;//随机生成字符数个数
	
		string get_a_target_string()
		{
			long target = 0;
			char buf[10];
	
			cout << "target (0~" << RAND_MAX << "): ";
			cin >> target;
			sprintf(buf, "%d", target);
			return string(buf);
		}
	
		void test_forward_list(const long& value)
		{
			cout << "\ntest_forward_list().......... \n";
	
			forward_list<string> c;
			char buf[10];
	
			clock_t timeStart = clock();
			for (long i = 0; i< value; ++i)
			{
				try {
					sprintf(buf, "%d", rand());
					c.push_front(string(buf));
				}
				catch (exception& p) {
					cout << "i=" << i << " " << p.what() << endl;
					abort();
				}
			}
			cout << "milli-seconds : " << (clock() - timeStart) << endl;
			cout << "forward_list.max_size()= " << c.max_size() << endl;  
			cout << "forward_list.front()= " << c.front() << endl;
	
	
			string target = get_a_target_string();
			timeStart = clock();
			auto pItem = find(c.begin(), c.end(), target);//调用stl标准库算法find()
			cout << "std::find(), milli-seconds : " << (clock() - timeStart) << endl;
	
			if (pItem != c.end())
				cout << "found, " << *pItem << endl;
			else
				cout << "not found! " << endl;
	
			timeStart = clock();
			c.sort();//调用forward_list内置sort()算法
			cout << "c.sort(), milli-seconds : " << (clock() - timeStart) << endl;
	
			c.clear();
		}
	}
	
	int main(void)
	{
		jj04::test_forward_list(jj04::ASIZE);
		return 0;
	}

编译运行结果如下：

![](https://i.imgur.com/fpew42j.png)

### (5)测试deque(VS2013) ###
deque是双向队列，两头可进可出，但stl对其内部实现是这样的：
![](https://i.imgur.com/9yp6q1e.png)

	#define _CRT_SECURE_NO_WARNINGS
	#include <deque>
	#include <stdexcept>
	#include <string>
	#include <cstdlib> //abort()
	#include <cstdio>  //snprintf()
	#include <iostream>
	#include <ctime> 
	#include <algorithm>
	using namespace std;
	
	namespace jj05
	{
		const long ASIZE = 50000L;//随机生成字符数个数
	
		string get_a_target_string()
		{
			long target = 0;
			char buf[10];
	
			cout << "target (0~" << RAND_MAX << "): ";
			cin >> target;
			sprintf(buf, "%d", target);
			return string(buf);
		}
	
		void test_deque(const long& value)
		{
			cout << "\ntest_deque().......... \n";
	
			deque<string> c;
			char buf[10];
	
			clock_t timeStart = clock();
			for (long i = 0; i< value; ++i)
			{
				try {
					sprintf(buf, "%d", rand());
					c.push_back(string(buf));
				}
				catch (exception& p) {
					cout << "i=" << i << " " << p.what() << endl;
					abort();
				}
			}
			cout << "milli-seconds : " << (clock() - timeStart) << endl;
			cout << "deque.size()= " << c.size() << endl;
			cout << "deque.front()= " << c.front() << endl;
			cout << "deque.back()= " << c.back() << endl;
			cout << "deque.max_size()= " << c.max_size() << endl;	//1073741821	
	
			string target = get_a_target_string();
			timeStart = clock();
			auto pItem = find(c.begin(), c.end(), target);
			cout << "std::find(), milli-seconds : " << (clock() - timeStart) << endl;
	
			if (pItem != c.end())
				cout << "found, " << *pItem << endl;
			else
				cout << "not found! " << endl;
	
			timeStart = clock();
			sort(c.begin(), c.end());
			cout << "sort(), milli-seconds : " << (clock() - timeStart) << endl;
	
			c.clear();
		}
	}
	
	int main(void)
	{
		jj05::test_deque(jj05::ASIZE);
		return 0;
	}

编译运行结果如下：

![](https://i.imgur.com/b8hSUpT.png)

### (6)测试queue(VS2013) ###
	#define _CRT_SECURE_NO_WARNINGS
	#include <queue>
	#include <stdexcept>
	#include <string>
	#include <cstdlib> //abort()
	#include <cstdio>  //snprintf()
	#include <iostream>
	#include <ctime> 
	using namespace std;
	
	namespace jj18
	{
		const long ASIZE = 50000L;//随机生成字符数个数
	
		void test_queue(const long& value)
		{
			cout << "\ntest_queue().......... \n";
	
			queue<string> c;
			char buf[10];
	
			clock_t timeStart = clock();
			for (long i = 0; i< value; ++i)
			{
				try {
					sprintf(buf, "%d", rand());
					c.push(string(buf));
				}
				catch (exception& p) {
					cout << "i=" << i << " " << p.what() << endl;
					abort();
				}
			}
			cout << "milli-seconds : " << (clock() - timeStart) << endl;
			cout << "queue.size()= " << c.size() << endl;
			cout << "queue.front()= " << c.front() << endl;
			cout << "queue.back()= " << c.back() << endl;
			c.pop();
			cout << "queue.size()= " << c.size() << endl;
			cout << "queue.front()= " << c.front() << endl;
			cout << "queue.back()= " << c.back() << endl;
		}
	}
	
	int main(void)
	{
		jj18::test_queue(jj18::ASIZE);
		return 0;
	}

编译运行结果如下：

![](https://i.imgur.com/QM0BTi8.png)

### (7)测试stack(VS2013) ###
	#define _CRT_SECURE_NO_WARNINGS
	#include <stack>
	#include <stdexcept>
	#include <string>
	#include <cstdlib> //abort()
	#include <cstdio>  //snprintf()
	#include <iostream>
	#include <ctime> 
	using namespace std;
	
	namespace jj17
	{
		const long ASIZE = 50000L;//随机生成字符数个数
	
		void test_stack(const long& value)
		{
			cout << "\ntest_stack().......... \n";
	
			stack<string> c;
			char buf[10];
	
			clock_t timeStart = clock();
			for (long i = 0; i< value; ++i)
			{
				try {
					sprintf(buf,"%d", rand());
					c.push(string(buf));
				}
				catch (exception& p) {
					cout << "i=" << i << " " << p.what() << endl;
					abort();
				}
			}
			cout << "milli-seconds : " << (clock() - timeStart) << endl;
			cout << "stack.size()= " << c.size() << endl;
			cout << "stack.top()= " << c.top() << endl;
			c.pop();
			cout << "stack.size()= " << c.size() << endl;
			cout << "stack.top()= " << c.top() << endl;
	
		}
	}
	
	int main(void)
	{
		jj17::test_stack(jj17::ASIZE);
		return 0;
	}

编译运行结果如下：

![](https://i.imgur.com/xkoHKZG.png)

### (8)测试multiset(VS2013) ###
multiset是可重复的set容器，内部实现是一棵红黑树，所以查询特别快：

![](https://i.imgur.com/aJiyXk5.png)

	#define _CRT_SECURE_NO_WARNINGS
	#include <set>
	#include <stdexcept>
	#include <string>
	#include <cstdlib> //abort()
	#include <cstdio>  //snprintf()
	#include <iostream>
	#include <ctime> 
	using namespace std;
	
	namespace jj06
	{
		const long ASIZE = 50000L;//随机生成字符数个数
	
		string get_a_target_string()
		{
			long target = 0;
			char buf[10];
	
			cout << "target (0~" << RAND_MAX << "): ";
			cin >> target;
			sprintf(buf, "%d", target);
			return string(buf);
		}
	
		void test_multiset(const long& value)
		{
			cout << "\ntest_multiset().......... \n";
	
			multiset<string> c;
			char buf[10];
			clock_t timeStart = clock();
			for (long i = 0; i< value; ++i)
			{
				try {
					sprintf(buf, "%d", rand());
					c.insert(string(buf));
				}
				catch (exception& p) {
					cout << "i=" << i << " " << p.what() << endl;
					abort();
				}
			}
			cout << "milli-seconds : " << (clock() - timeStart) << endl;
			cout << "multiset.size()= " << c.size() << endl;
			cout << "multiset.max_size()= " << c.max_size() << endl;	//214748364
	
			string target = get_a_target_string();
			{
				timeStart = clock();
				auto pItem = find(c.begin(), c.end(), target);	//比 c.find(...) 慢很多	
				cout << "std::find(), milli-seconds : " << (clock() - timeStart) << endl;
				if (pItem != c.end())
					cout << "found, " << *pItem << endl;
				else
					cout << "not found! " << endl;
			}
	
			{
				timeStart = clock();
				auto pItem = c.find(target);		//比 std::find(...) 快很多							
				cout << "c.find(), milli-seconds : " << (clock() - timeStart) << endl;
				if (pItem != c.end())
					cout << "found, " << *pItem << endl;
				else
					cout << "not found! " << endl;
			}
	
			c.clear();
		}
	}
	
	int main(void)
	{
		jj06::test_multiset(jj06::ASIZE);
		return 0;
	}

编译运行结果如下：

![](https://i.imgur.com/txLd5tf.png)

mulitset内置的find()算法比STL标准库提供的要快很多。

### (9)测试multimap(VS2013) ###
multimap是可重复的map容器，内部实现是一棵红黑树，所以查询特别快：

![](https://i.imgur.com/4RJ7ODz.png)

	#define _CRT_SECURE_NO_WARNINGS
	#include <map>
	#include <stdexcept>
	#include <string>
	#include <cstdlib> //abort()
	#include <cstdio>  //snprintf()
	#include <iostream>
	#include <ctime> 
	using namespace std;
	
	namespace jj07
	{
		const long ASIZE = 50000L;//随机生成字符数个数
	
		long get_a_target_long()
		{
			long target = 0;
	
			cout << "target (0~" << RAND_MAX << "): ";
			cin >> target;
			return target;
		}
	
		void test_multimap(const long& value)
		{
			cout << "\ntest_multimap().......... \n";
	
			multimap<long, string> c;
			char buf[10];
	
			clock_t timeStart = clock();
			for (long i = 0; i< value; ++i)
			{
				try {
					sprintf(buf, "%d", rand());
					//multimap 不可使用 [] 做 insertion 
					c.insert(pair<long, string>(i, buf));
				}
				catch (exception& p) {
					cout << "i=" << i << " " << p.what() << endl;
					abort();
				}
			}
			cout << "milli-seconds : " << (clock() - timeStart) << endl;
			cout << "multimap.size()= " << c.size() << endl;
			cout << "multimap.max_size()= " << c.max_size() << endl;	//178956970	
	
			long target = get_a_target_long();
			timeStart = clock();
			auto pItem = c.find(target);
			cout << "c.find(), milli-seconds : " << (clock() - timeStart) << endl;
			if (pItem != c.end())
				cout << "found, value=" << (*pItem).second << endl;
			else
				cout << "not found! " << endl;
	
			c.clear();
		}
	}
	
	int main(void)
	{
		jj07::test_multimap(jj07::ASIZE);
		return 0;
	}

编译运行结果如下：

![](https://i.imgur.com/9YG3f1U.png)

需要注意的是multimap插入值需要以键值对的方式插入c.insert(pair<long, string>(i, buf))，而不能使用[]方式。

### (10)测试unordered_multiset(VS2013) ###
unordered_multiset的内部是使用hash来实现的：

![](https://i.imgur.com/XVAvxJH.png)

	#define _CRT_SECURE_NO_WARNINGS
	#include <unordered_set>
	#include <stdexcept>
	#include <string>
	#include <cstdlib> //abort()
	#include <cstdio>  //snprintf()
	#include <iostream>
	#include <ctime> 
	using namespace std;
	
	namespace jj08
	{
		const long ASIZE = 50000L;//随机生成字符数个数
	
		string get_a_target_string()
		{
			long target = 0;
			char buf[10];
	
			cout << "target (0~" << RAND_MAX << "): ";
			cin >> target;
			sprintf(buf, "%d", target);
			return string(buf);
		}
	
		void test_unordered_multiset(const long& value)
		{
			cout << "\ntest_unordered_multiset().......... \n";
	
			unordered_multiset<string> c;
			char buf[10];
	
			clock_t timeStart = clock();
			for (long i = 0; i< value; ++i)
			{
				try {
					sprintf(buf, "%d", rand());
					c.insert(string(buf));
				}
				catch (exception& p) {
					cout << "i=" << i << " " << p.what() << endl;
					abort();
				}
			}
			cout << "milli-seconds : " << (clock() - timeStart) << endl;
			cout << "unordered_multiset.size()= " << c.size() << endl;
			cout << "unordered_multiset.max_size()= " << c.max_size() << endl;	//357913941
			cout << "unordered_multiset.bucket_count()= " << c.bucket_count() << endl;
			cout << "unordered_multiset.load_factor()= " << c.load_factor() << endl;
			cout << "unordered_multiset.max_load_factor()= " << c.max_load_factor() << endl;
			cout << "unordered_multiset.max_bucket_count()= " << c.max_bucket_count() << endl;
			for (unsigned i = 0; i< 20; ++i) {
				cout << "bucket #" << i << " has " << c.bucket_size(i) << " elements.\n";
			}
	
			string target = get_a_target_string();
			{
				timeStart = clock();
				auto pItem = find(c.begin(), c.end(), target);	//比 c.find(...) 慢很多	
				cout << "std::find(), milli-seconds : " << (clock() - timeStart) << endl;
				if (pItem != c.end())
					cout << "found, " << *pItem << endl;
				else
					cout << "not found! " << endl;
			}
	
			{
				timeStart = clock();
				auto pItem = c.find(target);		//比 std::find(...) 快很多							
				cout << "c.find(), milli-seconds : " << (clock() - timeStart) << endl;
				if (pItem != c.end())
					cout << "found, " << *pItem << endl;
				else
					cout << "not found! " << endl;
			}
	
			c.clear();
		}
	}
	
	int main(void)
	{
		jj08::test_unordered_multiset(jj08::ASIZE);
		return 0;
	}

编译运行结果如下：

![](https://i.imgur.com/HUnZiVx.png)

### (11)测试unordered_multimap(VS2013) ###
unordered_multimap的内部是使用hash来实现的：
![](https://i.imgur.com/LbVY8BQ.png)

	#define _CRT_SECURE_NO_WARNINGS
	#include <unordered_map>
	#include <stdexcept>
	#include <string>
	#include <cstdlib> //abort()
	#include <cstdio>  //snprintf()
	#include <iostream>
	#include <ctime> 
	using namespace std;
	
	namespace jj09
	{
		const long ASIZE = 50000L;//随机生成字符数个数
	
		long get_a_target_long()
		{
			long target = 0;
	
			cout << "target (0~" << RAND_MAX << "): ";
			cin >> target;
			return target;
		}
	
		void test_unordered_multimap(const long& value)
		{
			cout << "\ntest_unordered_multimap().......... \n";
	
			unordered_multimap<long, string> c;
			char buf[10];
	
			clock_t timeStart = clock();
			for (long i = 0; i< value; ++i)
			{
				try {
					sprintf(buf,"%d", rand());
					//multimap 不可使用 [] 進行 insertion 
					c.insert(pair<long, string>(i, buf));
				}
				catch (exception& p) {
					cout << "i=" << i << " " << p.what() << endl;
					abort();
				}
			}
			cout << "milli-seconds : " << (clock() - timeStart) << endl;
			cout << "unordered_multimap.size()= " << c.size() << endl;
			cout << "unordered_multimap.max_size()= " << c.max_size() << endl;	//357913941	
	
			long target = get_a_target_long();
			timeStart = clock();
			auto pItem = c.find(target);
			cout << "c.find(), milli-seconds : " << (clock() - timeStart) << endl;
			if (pItem != c.end())
				cout << "found, value=" << (*pItem).second << endl;
			else
				cout << "not found! " << endl;
		}
	}
	
	int main(void)
	{
		jj09::test_unordered_multimap(jj09::ASIZE);
		return 0;
	}

编译运行结果如下：

![](https://i.imgur.com/dS2M9V4.png)

### (12)测试set(VS2013) ###
set中元素不可重复，内部实现是一棵红黑树，所以查询特别快：

![](https://i.imgur.com/aJiyXk5.png)

	#define _CRT_SECURE_NO_WARNINGS
	#include <set>
	#include <stdexcept>
	#include <string>
	#include <cstdlib> //abort()
	#include <cstdio>  //snprintf()
	#include <iostream>
	#include <ctime> 
	using namespace std;
	
	namespace jj13
	{
		const long ASIZE = 50000L;//随机生成字符数个数
	
		string get_a_target_string()
		{
			long target = 0;
			char buf[10];
	
			cout << "target (0~" << RAND_MAX << "): ";
			cin >> target;
			sprintf(buf, "%d", target);
			return string(buf);
		}
	
		void test_set(const long& value)
		{
			cout << "\ntest_set().......... \n";
	
			set<string> c;
			char buf[10];
	
			clock_t timeStart = clock();
			for (long i = 0; i< value; ++i)
			{
				try {
					sprintf(buf, "%d", rand());
					c.insert(string(buf));
				}
				catch (exception& p) {
					cout << "i=" << i << " " << p.what() << endl;
					abort();
				}
			}
			cout << "milli-seconds : " << (clock() - timeStart) << endl;
			cout << "set.size()= " << c.size() << endl;
			cout << "set.max_size()= " << c.max_size() << endl;	   //214748364
	
			string target = get_a_target_string();
			{
				timeStart = clock();
				auto pItem = find(c.begin(), c.end(), target);	//比 c.find(...) 慢很多	
				cout << "std::find(), milli-seconds : " << (clock() - timeStart) << endl;
				if (pItem != c.end())
					cout << "found, " << *pItem << endl;
				else
					cout << "not found! " << endl;
			}
	
			{
				timeStart = clock();
				auto pItem = c.find(target);		//比 std::find(...) 快很多							
				cout << "c.find(), milli-seconds : " << (clock() - timeStart) << endl;
				if (pItem != c.end())
					cout << "found, " << *pItem << endl;
				else
					cout << "not found! " << endl;
			}
		}
	}
	
	int main(void)
	{
		jj13::test_set(jj13::ASIZE);
		return 0;
	}

编译运行结果如下：

![](https://i.imgur.com/YkHaNKZ.png)

从上面结果可以看出set元素不重复，初始化输入元素有50000个，但最后set只插入了25688个元素，重复元素不予考虑。

### (13)测试map(VS2013) ###
map内部实现是一棵红黑树，所以查询特别快：

![](https://i.imgur.com/f7gxX1b.png)
	
与multimap不同的是，map还可以通过c[i] = string(buf)方式插值。

	#define _CRT_SECURE_NO_WARNINGS
	#include <map>
	#include <stdexcept>
	#include <string>
	#include <cstdlib> //abort()
	#include <cstdio>  //snprintf()
	#include <iostream>
	#include <ctime> 
	using namespace std;
	
	namespace jj14
	{
		const long ASIZE = 50000L;//随机生成字符数个数
	
		long get_a_target_long()
		{
			long target = 0;
	
			cout << "target (0~" << RAND_MAX << "): ";
			cin >> target;
			return target;
		}
	
		void test_map(const long& value)
		{
			cout << "\ntest_map().......... \n";
	
			map<long, string> c;
			char buf[10];
	
			clock_t timeStart = clock();
			for (long i = 0; i< value; ++i)
			{
				try {
					sprintf(buf, "%d", rand());
					c[i] = string(buf);
				}
				catch (exception& p) {
					cout << "i=" << i << " " << p.what() << endl;
					abort();
				}
			}
			cout << "milli-seconds : " << (clock() - timeStart) << endl;
			cout << "map.size()= " << c.size() << endl;
			cout << "map.max_size()= " << c.max_size() << endl;		//178956970
	
			long target = get_a_target_long();
			timeStart = clock();
			auto pItem = c.find(target);
			cout << "c.find(), milli-seconds : " << (clock() - timeStart) << endl;
			if (pItem != c.end())
				cout << "found, value=" << (*pItem).second << endl;
			else
				cout << "not found! " << endl;
	
			c.clear();
		}
	}
	
	int main(void)
	{
		jj14::test_map(jj14::ASIZE);
		return 0;
	}

编译运行结果如下：

![](https://i.imgur.com/J9T4RJV.png)

### (14)测试unordered_set(VS2013) ###
unordered_set的内部是使用hash来实现的：

![](https://i.imgur.com/XVAvxJH.png)

	#define _CRT_SECURE_NO_WARNINGS
	#include <unordered_set>
	#include <stdexcept>
	#include <string>
	#include <cstdlib> //abort()
	#include <cstdio>  //snprintf()
	#include <iostream>
	#include <ctime> 
	using namespace std;
	
	namespace jj15
	{
		const long ASIZE = 50000L;//随机生成字符数个数
	
		string get_a_target_string()
		{
			long target = 0;
			char buf[10];
	
			cout << "target (0~" << RAND_MAX << "): ";
			cin >> target;
			sprintf(buf, "%d", target);
			return string(buf);
		}
	
		void test_unordered_set(const long& value)
		{
			cout << "\ntest_unordered_set().......... \n";
	
			unordered_set<string> c;
			char buf[10];
	
			clock_t timeStart = clock();
			for (long i = 0; i< value; ++i)
			{
				try {
					sprintf(buf, "%d", rand());
					c.insert(string(buf));
				}
				catch (exception& p) {
					cout << "i=" << i << " " << p.what() << endl;
					abort();
				}
			}
			cout << "milli-seconds : " << (clock() - timeStart) << endl;
			cout << "unordered_set.size()= " << c.size() << endl;
			cout << "unordered_set.max_size()= " << c.max_size() << endl;  //357913941
			cout << "unordered_set.bucket_count()= " << c.bucket_count() << endl;
			cout << "unordered_set.load_factor()= " << c.load_factor() << endl;
			cout << "unordered_set.max_load_factor()= " << c.max_load_factor() << endl;
			cout << "unordered_set.max_bucket_count()= " << c.max_bucket_count() << endl;
			for (unsigned i = 0; i< 20; ++i) {
				cout << "bucket #" << i << " has " << c.bucket_size(i) << " elements.\n";
			}
	
			string target = get_a_target_string();
			{
				timeStart = clock();
				auto pItem = find(c.begin(), c.end(), target);	//比 c.find(...) 慢很多	
				cout << "std::find(), milli-seconds : " << (clock() - timeStart) << endl;
				if (pItem != c.end())
					cout << "found, " << *pItem << endl;
				else
					cout << "not found! " << endl;
			}
	
			{
				timeStart = clock();
				auto pItem = c.find(target);		//比 std::find(...) 快很多							
				cout << "c.find(), milli-seconds : " << (clock() - timeStart) << endl;
				if (pItem != c.end())
					cout << "found, " << *pItem << endl;
				else
					cout << "not found! " << endl;
			}
		}
	}
	
	int main(void)
	{
		jj15::test_unordered_set(jj15::ASIZE);
		return 0;
	}

编译运行结果如下：

![](https://i.imgur.com/CzBSOwP.png)

### (15)测试unordered_map(VS2013) ###
unordered_map的内部是使用hash来实现的：

![](https://i.imgur.com/LbVY8BQ.png)

	#define _CRT_SECURE_NO_WARNINGS
	#include <unordered_map>
	#include <stdexcept>
	#include <string>
	#include <cstdlib> //abort()
	#include <cstdio>  //snprintf()
	#include <iostream>
	#include <ctime> 
	using namespace std;
	
	namespace jj16
	{
		const long ASIZE = 50000L;//随机生成字符数个数
	
		long get_a_target_long()
		{
			long target = 0;
	
			cout << "target (0~" << RAND_MAX << "): ";
			cin >> target;
			return target;
		}
	
		void test_unordered_map(const long& value)
		{
			cout << "\ntest_unordered_map().......... \n";
	
			unordered_map<long, string> c;
			char buf[10];
	
			clock_t timeStart = clock();
			for (long i = 0; i< value; ++i)
			{
				try {
					sprintf(buf, "%d", rand());
					c[i] = string(buf);
				}
				catch (exception& p) {
					cout << "i=" << i << " " << p.what() << endl;
					abort();
				}
			}
			cout << "milli-seconds : " << (clock() - timeStart) << endl;
			cout << "unordered_map.size()= " << c.size() << endl;	//357913941
			cout << "unordered_map.max_size()= " << c.max_size() << endl;
	
			long target = get_a_target_long();
			timeStart = clock();
			//! auto pItem = find(c.begin(), c.end(), target);	//map 不適用 std::find() 			
			auto pItem = c.find(target);
	
			cout << "c.find(), milli-seconds : " << (clock() - timeStart) << endl;
			if (pItem != c.end())
				cout << "found, value=" << (*pItem).second << endl;
			else
				cout << "not found! " << endl;
		}
	}
	
	int main(void)
	{
		jj16::test_unordered_map(jj16::ASIZE);
		return 0;
	}

编译运行结果如下：

![](https://i.imgur.com/eYs8cni.png)