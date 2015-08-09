## Problem 1

### Solution	

	规则：
		grid[i][j] == -1, 不能通过；每个格子只能被访问一次
		从最左边的任意格子开始，到最右边的一个格子停止，遍历的方向：up/down/right
		当到达一列的低端或顶端都可以被传送到另一端，此时分数变为0
	目标：
		获得最高分数
	方法：
		dfs, backtracking

### Code

	#include <iostream>
	#include <vector>
	using namespace std;
	
	
	void gameDfs(vector<vector<int> >& grid, int row, int col, int cur, int& res) {
		int n = grid.size();
		int m = grid[0].size();
		if (col == m) {
			res = max(res, cur);
			return;
		}
		if (row == -1 || row == n) {
			row = (n + row) % n;
			cur = 0;
		}
		if (grid[row][col] == -1 || grid[row][col] == -2) return; 
		int temp = grid[row][col];
		grid[row][col] = -2;
		gameDfs(grid, row + 1, col, cur + temp, res);
		gameDfs(grid, row - 1, col, cur + temp, res);
		gameDfs(grid, row, col + 1, cur + temp, res);
		grid[row][col] = temp;
	}
	int highestScore(vector<vector<int> >& grid) {
		int res = -1;
		for (int i = 0; i < grid.size(); ++i) {
			gameDfs(grid, i, 0, 0, res);
		}
		return res;
	}
	int main()
	{
		int n, m;
		cin >> n >> m;
		vector<vector<int> > grid(n, vector<int>(m));
		for (int i = 0; i < n; ++i) {
			for (int j = 0; j  < m; ++j) {
				cin >> grid[i][j];
			}
		}
		cout << highestScore(grid) << endl;
		system("pause");
		return 0;
	} 

### 注意事项

	dfs+backtracking的题目，按照模板去做：
		1. 写好主调用程序
		2. 终止条件
	判断条件放在终止条件中，代码会很简洁

## Problem 2

### Solution

	题意：更新最短路径，求最短路径

### Code
	
	#include <iostream>
	#include <vector>
	using namespace std;
	
	void update(const vector<vector<int> >& highways, int start, int cur, vector<int>& dis) {
		dis[start] = cur; 
		for (int i = 0; i < highways[start].size(); ++i) {
			update(highways, highways[start][i], cur + 1, dis);
		}
	}
	
	int main()
	{
		int n, m;
		cin >> n >>m;
		vector<vector<int> > highays(n, vector<int>());
		int from = 0;
		int to = 0;
		for (int i = 0; i < n - 1; ++i) {
			cin >> from >> to;
			highays[from - 1].push_back(to - 1);
		}
		vector<int> dis(n, 0);
		update(highays, 0, 0, dis);
		int q = 0;
		int c = 0;
		for (int i = 0; i < m; ++i) {
			cin >> q >> c;
			if (q == 1) {
				update(highays, c - 1, 0, dis);
			} else {
				cout << dis[c - 1] << endl;
			}
		}
		system("pause");
		return 0;
	}

### 注意事项

	注意题目中的条件：只有(n - 1)条边，所以题目的存储空间和算法的时间空间复杂度都相应降低很多

## Problem 3

### Code
	
	#include <stdio.h>
	#include <memory.h>
	using namespace std;
	
	const int MAXV = 10010;
	
	const char str_op[] = "dulr";
	int fx[] = {1, -1, 0, 0};
	int fy[] = {0, 0, -1, 1};
	
	inline int id(char c) {
		static const char op[] = "dulr";
		if (c == op[0]) return 0;
		if (c == op[1]) return 1;
		if (c == op[2]) return 2;
		if (c == op[3]) return 3;
	}
	
	char op[MAXV];
	int len;
	char mat[105][105];
	int n, m, s;
	
	int sx, sy, vx, vy, ex, ey;
	
	void init() {
		for(int i = 1; i <= n; ++i)
			for(int j = 1; j <= m; ++j) {
				if(mat[i][j] == '1') sx = i, sy = j;
				if(mat[i][j] == '2') ex = i, ey = j;
				if(mat[i][j] == '3') vx = i, vy = j;
			}
	}
	
	bool solve() {
		int x1 = sx, y1 = sy, x2 = vx, y2 = vy;
		for(int i = 0; i < len; ++i) {
			int f = id(op[i]);
			int new_x = x1 + fx[f], new_y = y1 + fy[f];
			if(new_x == x2 && new_y == y2) {
				int pp = x2 + fx[f], qq = y2 + fy[f];
				if(mat[pp][qq] != '4') {
					x1 = new_x, y1 = new_y;
					x2 = pp,    y2 = qq;
				}
			} else if(mat[new_x][new_y] != '4') {
				x1 = new_x, y1 = new_y;
			}
			if(x2 == ex && y2 == ey) return true;
		}
		return false;
	}
	
	int main() {
		memset(mat, '4', sizeof(mat));
		scanf("%d%d%d", &m, &n, &s);
		for(int i = 1; i <= n; ++i)
			for(int j = 1; j <= m; ++j) scanf(" %c", &mat[i][j]);
		init();
		while(s--) {
			scanf("%d %s", &len, op);
			puts(solve() ? "YES" : "NO");
		}
	}

### 注意事项

	过程搞清楚：
		1. 玩家下一个移动位置到箱子，才可以玩家与箱子同时移动一格
		2. 玩家的下一个移动位置不是箱子的话，只有玩家移动
		3. 其他情况均不移动

## Problem 4

### Code

	#include <cstdio>
	#include <algorithm>
	#include <cstring>
	#include <iostream>
	using namespace std;
	typedef long long LL;
	
	int f[8][3] = {
		{0, 1, 2},
		{3, 4, 5},
		{6, 7, 8},
		{0, 3, 6},
		{1, 4, 7},
		{2, 5, 8},
		{0, 4, 8},
		{2, 4, 6}
	};
	
	char mat[10];
	int wino[8], winx[8];
	int T;
	
	void build_check(int win[], char c) {
		for(int i = 0; i < 8; ++i) {
			win[i] = 1;
			for(int j = 0; j < 3; ++j)
				if(mat[f[i][j]] != c) win[i] = 0;
		}
	}
	int build_count(char c) {
		return count(mat, mat + 9, c);
	}
	bool next_win(char c) {
		int win[8];
		for(int i = 0; i < 9; ++i) if(mat[i] == '_') {
			mat[i] = c;
			build_check(win, c);
			if(count(win, win + 8, 1)) return true;
			mat[i] = '_';
		}
		return false;
	}
	int solve() {
		int xcnt = build_count('X'), ocnt = build_count('O');
		if(xcnt != ocnt && xcnt - 1 != ocnt) return puts("Invalid");
	
		build_check(winx, 'X');
		build_check(wino, 'O');
		if(count(winx, winx + 8, 1) > 0 && count(wino, wino + 8, 1) > 0) return puts("Invalid");
		if(count(winx, winx + 8, 1) > 0 && xcnt == ocnt) return puts("Invalid");
		if(count(wino, wino + 8, 1) > 0 && xcnt - 1 == ocnt) return puts("Invalid");
	
		if(count(winx, winx + 8, 1) > 0) return puts("X win");
		if(count(wino, wino + 8, 1) > 0) return puts("O win");
	
		if(build_count('_') == 0) return puts("Draw");
	
		if(next_win(xcnt == ocnt ? 'X' : 'O')) return puts("Next win");
		return puts("Next cannot win");
	}
	int main() {
		scanf("%d", &T);
		while(T--) {
			scanf("%s", mat);
			scanf("%s", mat + 3);
			scanf("%s", mat + 6);
			solve();
		}
	}
	
备注：
后面两题代码来源 :[hihocoder 网易游戏2016实习生招聘在线笔试 解题报告](http://www.cnblogs.com/oyking/p/4404653.html)
	

