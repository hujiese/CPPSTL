## 1. 迭代器分类 ##
![](https://i.imgur.com/nwIUo9E.png)
![](https://i.imgur.com/9Y2pYH9.png)
![](https://i.imgur.com/wqs2rm5.png)
![](https://i.imgur.com/smGIoVH.png)
![](https://i.imgur.com/AzSE5JX.png)
![](https://i.imgur.com/u7detx0.png)
![](https://i.imgur.com/5ObIf8v.png)
![](https://i.imgur.com/2QSbyyf.png)
![](https://i.imgur.com/Q5M8HiN.png)
![](https://i.imgur.com/sO9249g.png)
![](https://i.imgur.com/BoQvLq9.png)
![](https://i.imgur.com/YtopuNn.png)
![](https://i.imgur.com/i9utbN2.png)

## 2. 算法源代码剖析（11个例子） ##
### （1）accumulate ###
![](https://i.imgur.com/zScSGbZ.png)

	#define _CRT_SECURE_NO_WARNINGS
	#include <iostream>     // std::cout
	#include <functional>   // std::minus
	#include <numeric>      // std::accumulate
	using namespace std;
	
	namespace jj34
	{
		int myfunc(int x, int y) { return x + 2 * y; }
	
		struct myclass {
			int operator()(int x, int y) { return x + 3 * y; }
		} myobj;
	
		void test_accumulate()
		{
			cout << "\ntest_accumulate().......... \n";
			int init = 100;
			int nums[] = { 10, 20, 30 };
	
			cout << "using default accumulate: ";
			cout << accumulate(nums, nums + 3, init);  //160
			cout << '\n';
	
			cout << "using functional's minus: ";
			cout << accumulate(nums, nums + 3, init, minus<int>()); //40
			cout << '\n';
	
			cout << "using custom function: ";
			cout << accumulate(nums, nums + 3, init, myfunc);	//220
			cout << '\n';
	
			cout << "using custom object: ";
			cout << accumulate(nums, nums + 3, init, myobj);	//280
			cout << '\n';
		}
	}
	
	int main(void)
	{
		jj34::test_accumulate();
		return 0;
	}


编译运行结果如下：

![](https://i.imgur.com/idNjlUy.png)

### （2）for_each ###
![](https://i.imgur.com/AhTgfLD.png)

	#define _CRT_SECURE_NO_WARNINGS
	#include <iostream>     // std::cout
	#include <algorithm>    // std::for_each
	#include <vector>       // std::vector
	using namespace std;
	
	namespace jj35
	{
		void myfunc(int i) {
			cout << ' ' << i;
		}
	
		struct myclass {
			void operator() (int i) { cout << ' ' << i; }
		} myobj;
	
		void test_for_each()
		{
			cout << "\ntest_for_each().......... \n";
	
			vector<int> myvec;
			myvec.push_back(10);
			myvec.push_back(20);
			myvec.push_back(30);
	
			for_each(myvec.begin(), myvec.end(), myfunc);
			cout << endl;		//output: 10 20 30
	
			for_each(myvec.begin(), myvec.end(), myobj);
			cout << endl;		//output: 10 20 30
	
			//since C++11, range-based for- statement
			for (auto& elem : myvec)
				elem += 5;
	
			for (auto elem : myvec)
				cout << ' ' << elem; 	//output: 15 25 35
		}
	}
	
	int main(void)
	{
		jj35::test_for_each();
		return 0;
	}


编译运行结果如下：

![](https://i.imgur.com/Wu2v6SJ.png)

### （3）replace、raplace_if和replace_copy ###
![](https://i.imgur.com/Ej3kWBW.png)


### （4）count和count_if ###
![](https://i.imgur.com/0oeMPDd.png)

### （5）find和find_if ###
![](https://i.imgur.com/WgpBdqR.png)

### （6）sort ###
![](https://i.imgur.com/VqC8m7h.png)

	#define _CRT_SECURE_NO_WARNINGS
	#include <iostream>     // std::cout
	#include <algorithm>    // std::sort
	#include <vector>       // std::vector
	#include <functional>
	using namespace std;
	
	namespace jj36
	{
		bool myfunc(int i, int j) { return (i<j); }
	
		struct myclass {
			bool operator() (int i, int j) { return (i<j); }
		} myobj;
	
		bool test_sort()
		{
			cout << "\ntest_sort().......... \n";
	
			int myints[] = { 32, 71, 12, 45, 26, 80, 53, 33 };
			vector<int> myvec(myints, myints + 8);          // 32 71 12 45 26 80 53 33
	
			// using default comparison (operator <):
			sort(myvec.begin(), myvec.begin() + 4);         //(12 32 45 71)26 80 53 33
	
			// using function as comp
			sort(myvec.begin() + 4, myvec.end(), myfunc); 	// 12 32 45 71(26 33 53 80)
	
			// using object as comp
			sort(myvec.begin(), myvec.end(), myobj);      //(12 26 32 33 45 53 71 80)
	
			// print out content:
			cout << "\nmyvec contains:";
			for (auto elem : myvec)		//C++11 range-based for statement
				cout << ' ' << elem; 	//output: 12 26 32 33 45 53 71 80
	
			// using reverse iterators and default comparison (operator <):
			sort(myvec.rbegin(), myvec.rend());
	
			// print out content:
			cout << "\nmyvec contains:";
			for (auto elem : myvec)		//C++11 range-based for statement
				cout << ' ' << elem; 	//output: 80 71 53 45 33 32 26 12    
	
			// using explicitly default comparison (operator <):
			sort(myvec.begin(), myvec.end(), less<int>());
	
			// print out content:
			cout << "\nmyvec contains:";
			for (auto elem : myvec)		//C++11 range-based for statement
				cout << ' ' << elem; 	//output: 12 26 32 33 45 53 71 80   
	
			// using another comparision criteria (operator >):
			sort(myvec.begin(), myvec.end(), greater<int>());
	
			// print out content:
			cout << "\nmyvec contains:";
			for (auto elem : myvec)		//C++11 range-based for statement
				cout << ' ' << elem; 	//output: 80 71 53 45 33 32 26 12 
	
			return true;
		}
	}
	
	int main(void)
	{
		jj36::test_sort();
		return 0;
	}

编译运行结果如下：

![](https://i.imgur.com/i3P1iwK.png)

### （7）关于reverse、iterator、rbegin()和rend() ###
![](https://i.imgur.com/L60kl3T.png)

### （8）binary_search ###
![](https://i.imgur.com/oWf4HOA.png)

## 3. 仿函数 ##
![](https://i.imgur.com/gjCrEvv.png)
![](https://i.imgur.com/CjmDq2p.png)
![](https://i.imgur.com/bg6A7Pa.png)
![](https://i.imgur.com/QB83KKh.png)

## 4. 适配器 ##
![](https://i.imgur.com/QsTkwmd.png)
![](https://i.imgur.com/4KAgdQG.png)


## 5. binder2nd ##
![](https://i.imgur.com/THABSKY.png)

## 6. notl ##
![](https://i.imgur.com/eCJUvnc.png)

## 7. 新适配器bind ##
![](https://i.imgur.com/o8zPo2o.png)
![](https://i.imgur.com/tVzBVTi.png)

## 8. reverse_iterator ##
![](https://i.imgur.com/RRCJL25.png)

## 9. inserter ##
![](https://i.imgur.com/EM5Syun.png)

## 10. ostream_iterator ##
![](https://i.imgur.com/3Uw83Qw.png)

## 11. istream_iterator ##
![](https://i.imgur.com/DOPz8Dn.png)
![](https://i.imgur.com/PsHru8W.png)
