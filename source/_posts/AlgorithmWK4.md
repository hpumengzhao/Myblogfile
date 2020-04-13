---
title: Algorithms Weekly 4
tags: Probelems
mathjax: true
---

## A TLE Week

<!---more--->

* **Nowcoder练习赛61**

  D.**最短路变短了**

  一个$n$个节点$m$条边的有向图，有$q$次查询，每次查询一条边$(u,v)$，如果把$(u,v)$反过来，判断$1$到$n$

  的最短路会不会变短。

  $(1≤n≤100000),(1≤m≤200000), (1≤q≤200000)$

  solution:

  先跑一边最短路，令$da_{i}$代表$1$到$i$的最短路，再反向建边以$n$为起点跑一边最短路，$db_{i}$为$i$到$n$的最短路，

  对于查询的$(u,v,w)$，判断是否$da_{v}+w+db_{u}<da_{n}$，如果满足，则最短路会变短。

  code:

  ```cpp
  /*
* @author:  codancer
* @createTime:  2020-04-10, 20:13:32
*/
#include<bits/stdc++.h>

using namespace std;
typedef long long ll;
const ll mod = 1e9+7;
#define pb push_back
#define fi first
#define se second
#define SZ(x) ((int)(x).size())
#define rep(i,a,b) for(int i=(a);i<=(b);i++)
#define fep(i,a,b) for(int i=(a);i>=(b);i--)
typedef vector<int> VI;
typedef vector<ll> VII;
typedef pair<int,int> pii;
const int N = 200010;
struct node{
	int id;
	ll dis;
};
int n,m,u,v,x;
ll w;
bool operator<(node a,node b){
	return a.dis>b.dis;
}
struct graphs{
	vector<pair<int,ll>> G[N];
	void addedge(int u,int v,ll w){
		G[u].pb({v,w});
	}
	ll dis[N];
	priority_queue<node> q;
	bool vis[N];
	void bfs(int st){
		memset(vis,0,sizeof(vis));
		for(int i=1;i<=n;i++){
			dis[i]=1e17;
		}
		dis[st]=0;
		q.push({st,0});
		while(!q.empty()){
			node rt=q.top();q.pop();
			int u=rt.id;
			if(vis[u]) continue;
			vis[u]=1;
			for(auto adj:G[u]){
				int v=adj.first;
				ll w=adj.second;
				if(dis[v]>dis[u]+w){
					dis[v]=dis[u]+w;
					q.push({v,dis[v]});
				}
			}
		}
	}
}A,B;
pair<pair<int,int>,ll> edges[N];
int main(){
	scanf("%d %d",&n,&m);
	rep(i,1,m){
		scanf("%d %d %lld",&u,&v,&w);
		edges[i]={{u,v},w};
		A.addedge(u,v,w);
		B.addedge(v,u,w);
	}
	A.bfs(1);
	B.bfs(n);
	ll ans=A.dis[n];
	int q;
	scanf("%d",&q);
	while(q--){
		scanf("%d",&x);
		int uu=edges[x].first.first;
		int vv=edges[x].first.second;
		ll ww=edges[x].second;
		ll fi=A.dis[vv];
		ll se=B.dis[uu];
		if(fi+se+ww<ans){
			puts("YES");
		}else{
			puts("NO");
		}
	}
	return 0;
}
  ```
  
* Leetcode 30days challenge

  **Middle of the Linked List**

  寻找链表的中间节点，这是一个非常经典的问题，设置两个指针，一个每次跳一步，另一个每次跳两步，直到快的那个为空，慢的那个所在的节点即为答案。

  代码：
  
  ```cpp
    /**
     * Definition for singly-linked list.
     * struct ListNode {
     *     int val;
     *     ListNode *next;
     *     ListNode(int x) : val(x), next(NULL) {}
     * };
     */
    class Solution {
    public:
        ListNode* middleNode(ListNode* head) {
            ListNode *A=head;
            ListNode *B=head;
            while(B!=NULL){
                B=B->next;
                if(B==NULL){
                    return A;
                }
                A=A->next;
                B=B->next;
            }
            return A;
        }
  };
    ```
  
* **给 N x 3 网格图涂色的方案数**

  题面：

  * 你有一个 $n \cdot  3$ 的网格图 grid ，你需要用 红，黄，绿 三种颜色之一给每一个格子上色，且确保相邻格子颜色不同（也就是有相同水平边或者垂直边的格子颜色不同）。给你网格图的行数 $n$ 。请你返回给 grid 涂色的方案数。由于答案可能会非常大，请你返回答案对 $10^9 + 7$ 取余的结果。

  解法:

  * 简单dp,枚举每一行的所有状态，按行转移即可，时间复杂度$O(27n)$。

  代码：

  ```cpp
    
    class Solution {
    public:
        long long dp[5005][30];
        int numOfWays(int n) {
            memset(dp,0,sizeof(dp));
            // string up,now;
            int up[3]={0},now[3]={0};
            for(int i=0;i<=25;i++){
                int x=i;
                int cnt=0;
                while(x){
                    up[cnt++]=x%3;
                    x/=3;
                }
                if(up[2]!=up[1]&&up[1]!=up[0]){
                    dp[1][i]=1;
                }
            }
            for(int i=2;i<=n;i++){
                for(int last=0;last<=25;last++){
                    for(int j=0;j<3;j++) up[j]=0;
                    bool ok=1;
                    int x=last;
                    int cnt=0;
                    while(x){
                        up[cnt++]=x%3;
                        x/=3;
                    }
                    if(up[2]!=up[1]&&up[1]!=up[0]){
                        for(int nxt=0;nxt<=25;nxt++){
                            for(int k=0;k<3;k++) now[k]=0;
                            int x=nxt;
                            int cnt=0;
                            while(x){
                                now[cnt++]=x%3;
                                x/=3;
                            }
                            if(now[2]!=now[1]&&now[1]!=now[0]){
                                if((now[0]!=up[0])&&(now[1]!=up[1])&&(now[2]!=up[2])){
                                    dp[i][nxt]+=dp[i-1][last];
                                    dp[i][nxt]%=1000000007;
                                }
                            }
                        }
                    }
                }
            }
            long long ans=0;
            for(int i=0;i<=25;i++){
                ans+=dp[n][i];
                ans%=1000000007;
            }
            return ans;
        }
    };
    ```

  

* **ABC162E.  Sum of gcd of Tuples (Hard)**

  题意:

  * 有一个未知序列$a$,长度为$n$,已知$a$中的每个元素都在$[1,k]$之内，这样的序列有$k^n$个，计算
    $$
    \sum\limits_{i=1}^{k^n}{gcd(a_{1},a_{2}...,a_{n})}
    $$

  解法：

  * 考虑$cnt_{i}$为$gcd$为$i$的所有序列个数，那么$a$中的每个元素必定是$i$的倍数，这样的序列有${\lfloor \frac{k}{i} \rfloor}^n$个，但是这样我们也会把$gcd$为$2i,3i,4*i...$也会统计上，因此还得减去$cnt_{j}(j|i)$，我们从$k$到$1$倒着枚举即可，时间复杂度为$O(n \cdot \sqrt n)$。

  代码：

  ```cpp
    /*
    * @author:  codancer
    * @createTime:  2020-04-12, 21:02:02
    */
    #include<bits/stdc++.h>
    
    using namespace std;
    typedef long long ll;
    const ll mod = 1e9+7;
    #define pb push_back
    #define fi first
    #define se second
    #define SZ(x) ((int)(x).size())
    #define rep(i,a,b) for(int i=(a);i<=(b);i++)
    #define fep(i,a,b) for(int i=(a);i>=(b);i--)
    typedef vector<int> VI;
    typedef vector<ll> VII;
    typedef pair<int,int> pii;
    ll qpow(ll a,ll b){
    	ll ans=1;
    	while(b){
    		if(b&1) ans=ans*a%mod;
    		a=a*a%mod;
    		b>>=1;
    	}
    	return ans%mod;	
    }
    ll cnt[500005];
    int main(){
    	ll n,k;
    	cin>>n>>k;
    	ll ans=0;
    	fep(i,k,1){
    		cnt[i]=qpow(k/i,n);
    		for(int j=2*i;j<=k;j+=i){
    			cnt[i]-=cnt[j];
    			cnt[i]+=mod;
    			cnt[i]%=mod;
    		}
    		ans+=((ll)i*cnt[i])%mod;
    		ans%=mod;
    	}
    	cout<<ans<<endl;
    	return 0;
    }
    ```

* **ABC162F.  Select Half**

  题意：

  * 从长度为$n$的数组$a$中选出$\lfloor \frac{n}{2} \rfloor$个不相邻的元素使他们和最大。

  解法：

  * 假设选择了前$i$个元素并且$a_{i}$被选择了,此时最少会有$\lfloor \frac{n+1}{2} \rfloor-1$个空格，类似于：

    XOXOXOXO

    XOXOXO（X是被选择的）

    令$dp_{i,j}$代表选择了$a_{i}$并且比$\lfloor \frac{i+1}{2} \rfloor-1$多使用了$j$个空格，$j$最大为$2$，此时的最大值。

    因此我们可以得出转移方程：

    $dp_{i,0}=dp_{i-2,0}+a_{i}$ (一个也不多，那么肯定是选择了第$i-2$个，接下来就是$i$)

    $dp_{i,1}=max(dp_{i-2,1},dp_{i-3,0})+a_{i}$(多一个空格，可能是从$i-2$之前就多了，或者多出来的就是$i-2$)

    $dp_{i,2}=max(dp_{i-2,2},dp_{i-3,1},dp_{i-4,0})+a_{i}$（读者可以自己分析，不再赘述，和$dp_{i,1}$的情况类似）

    转移完之后考虑$n$的奇偶性，如果$n$为奇数。

    假设最后选了第$n$个，结果为$dp_{n,2}$(肯定多$2$个)

    XOXOOOX(本来是最少用$3-1=2$个空格，现在用了$4$个)

    假设最后选择了第$(n-1)$个，那么结果为$dp_{n-1,1}$(肯定多$  1 $个)

    XOXOOXO(本来是最少用$3-1=2$个空格，现在用了$ 3 $个)

    假设最后选择了第$(n-2)$个，那么结果为$dp_{n-2,0}$

    XOXOXOO(只用了两个)

    偶数情况类似，答案取$max(dp_{n-1,0},dp_{n,1})$，读者可以自己讨论。

    

    ```cpp
    /*
    * @author:  codancer
    * @createTime:  2020-04-13, 10:42:14
    */
    #include<bits/stdc++.h>
    
    using namespace std;
    typedef long long ll;
    const ll mod = 1e9+7;
    #define pb push_back
    #define fi first
    #define se second
    #define SZ(x) ((int)(x).size())
    #define rep(i,a,b) for(int i=(a);i<=(b);i++)
    #define fep(i,a,b) for(int i=(a);i>=(b);i--)
    typedef vector<int> VI;
    typedef vector<ll> VII;
    typedef pair<int,int> pii;
    const int N = 2e5+100;
    ll dp[N][3];
    ll a[N];
    int main(){
    	int n;
    	cin>>n;
    	rep(i,1,n){
    		cin>>a[i];
    	}
    	rep(i,1,n){
    		rep(j,0,2){
    			dp[i][j]=-1e18;
    		}
    	}
    	rep(i,1,3) dp[i][i-1]=a[i];
    	rep(i,3,n){
    		if(i>2){
    			dp[i][0]=max(dp[i][0],dp[i-2][0])+a[i];
    			dp[i][1]=max(dp[i][1],dp[i-2][1]+a[i]);
    			dp[i][2]=max(dp[i][2],dp[i-2][2]+a[i]);
    		}
    		if(i>3){
    			dp[i][1]=max(dp[i][1],dp[i-3][0]+a[i]);
    			dp[i][2]=max(dp[i][2],dp[i-3][1]+a[i]);
    		}
    		if(i>4){
    			dp[i][2]=max(dp[i][2],dp[i-4][0]+a[i]);
    		}
    	}
    	ll ans=-1e18;
    	if(n&1){
    		ans=max(ans,dp[n][2]);
    		if(n>1){
    			ans=max(ans,dp[n-1][1]);
    		}
    		if(n>2){
    			ans=max(ans,dp[n-2][0]);
    		}
    	}else{
    		ans=max(ans,dp[n][1]);
    		if(n>1) ans=max(ans,dp[n-1][0]);
    	}
    	cout<<ans<<endl;
    	return 0;
    }
    ```

    

    

    

    

    