# 1.编程题

## 1.1.题目一：[公共子串计算](https://www.nowcoder.com/practice/98dc82c094e043ccb7e0570e5342dd1b?tpId=37&tqId=21298&ru=/exam/oj)

这道题目想多了，直接暴力解决把，果然是要先思考暴力解决再考虑优化。

```cpp
#include <iostream>
#include <string>
#include <vector>
using namespace std;

int getMaxComSubStr(const string& str1, const string& str2)
{
	int len1 = str1.size();
	int len2 = str2.size();
	vector<vector<int>> msc(len1, vector<int>(len2, 0));
	int max_len = 0;
	for (int i = 0; i < len1; i++)
	{
		for (int j = 0; j < len2; j++)
		{
			if (str1[i] == str2[j])
			{
				if (i >= 1 && j >= 1)
				{
					msc[i][j] = msc[i - 1][j - 1] + 1;
				}
				else
				{
					msc[i][j] = 1;
				}
				max_len = max(max_len, msc[i][j]);
			}
		}
	}
	return max_len;
}
int main()
{
	string str1, str2;
	while (cin >> str1 >> str2)
	{
		int max_len = getMaxComSubStr(str1, str2);
		cout << max_len;
	}
	return 0;
}
```

# 2.选择题

## 2.1.题目一：分支节点的概念混淆

若将关键字`[1,2,3,4,5,6,7]` 依次插入到初始为空的平衡二叉树`T`中，则`T`中平衡因子为`0`的分支结点的个数是多少？

>   解析：这里的平衡二叉树就是二叉平衡搜索树，旋转到不是什么问题，主要是注意分支结点包含根节点，但不包含叶子结点。