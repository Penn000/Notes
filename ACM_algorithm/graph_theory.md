## 链式前向星

```cpp
const int N = 1010;
const int M = 110000;
const int INF = 0x3f3f3f3f;

struct Edge{
    int to, w, next;
}edges[M];

int head[N], tot;

void init(){
    memset(head, -1, sizeof(head));
    tot = 0;
}

void add_edges(int u, int v, int w){
    edges[tot].to = v;
    edges[tot].w = w;
    edges[tot].next = head[u];
    head[u] = tot++;
}

// 遍历从u出发的所有边
for(int i = head[u]; i != -1; i = edges[i].next){
    int v = edges[i].to;
    int w = edges[i].w;
}
```

## 最短路

### dijkstra

```cpp
int dist[N];
bool vis[N];
// s: 起点; n: 点的个数; 下标从1开始
void dijkstra(int s, int n){
    memset(vis, 0, sizeof(vis));
    vis[s] = 1;
    for(int i = 1; i <= n; i++){
        dist[i] = INF;
    }
    dist[s] = 0;
    for(int i = head[s]; i != -1; i = edges[i].next){
        int v = edges[i].to;
        int w = edges[i].w;
        dist[v] = w;
    }
    int mindist, u;
    for(int ii = 0; ii < n-1; ii++){
        mindist = INF;
        for(int j = 1; j <= n; j++){
            if(!vis[j] && dist[j] < mindist){
                u = j;
                mindist = dist[j];
            }
        }
        vis[u] = 1;
        for(int i = head[u]; i != -1; i = edges[i].next){
            int v = edges[i].to;
            int w = edges[i].w;
            if(dist[v] > dist[u]+w)
                dist[v] = dist[u]+w;
        }
    }
}
```

优先队列优化的dijkstra

```cpp
struct Node{
    int dist, idx;
    Node(int _dist, int _idx):dist(_dist), idx(_idx){}
    bool operator<(const Node& node) const{
        return dist<node.dist;
    }
};

bool vis[N];
int dist[N];
priority_queue<Node> pq;

void dijkstra(int s, int n){
    for(int i = 1; i <= n; i++){
        dist[i] = INF;
    }
    dist[s] = 0;
    while(!pq.empty())pq.pop();
    Node tmp(dist[s], s);
        pq.push(tmp);
    while(!pq.empty()){
        Node tmp = pq.top();
        pq.pop();
        int u = tmp.idx;
        for(int i = head[u]; i != -1; i = edges[i].next){
            int v = edges[i].to;
            int w = edges[i].w;
            if(dist[v] > dist[u]+w){
                dist[v] = dist[u]+w;
                Node node(dist[v], v);
                pq.push(node);
            }
        }
    }
}
```

### SPFA

```cpp
// cnt[u]记录u入队次数，判负环用
int dis[N], cnt[N];
bool vis[N];
bool spfa(int s, int n){
    memset(vis, false, sizeof(vis));
    memset(dis, INF, sizeof(dis));
    memset(cnt, 0, sizeof(cnt));
    vis[s] = true;
    dis[s] = 0;
    cnt[s] = 1;
    queue<int> q;
    q.push(s);
    while(!q.empty()){
        int u = q.front();
        q.pop();
        vis[u] = false;
        for(int i = head[u]; i != -1; i = edge[i].next){
            int v = edge[i].to;
            int w = edge[i].w;
            if(dis[v] > dis[u] + w){
                dis[v] = dis[u] + w;
                if(!vis[v]){
                    vis[v] = true;
                    q.push(v);
                    if(++cnt[v] > n)return false;
                }
            }
        }
    }
    return true;
}
```

### Floyd

```cpp
int G[N][N], D[N][N], P[N][N];

void floyd(){
    // 初始化
    for(int i = 1; i <= n; i++)
        for(int j = 1; j <= n; j++){
            D[i][j] = G[i][j];
            P[i][j] = j;
        }
    
    // 核心代码            
    for(k=1; k<=n; k++)
        for(i=1; i<=n; i++)
            for(j=1; j<=n;j++)
                if(D[i][j]>D[i][k]+D[k][j]){
                    D[i][j] = D[i][k]+D[k][j];
                    P[i][j] = P[i][k];
                }
}

//打印最短路径
void print_path(int u, int v) {
    while(u != v) {
        cout<<u<<" ";
        u = P[u][v];
    }
    cout<<u<<endl;s
}

```

## 生成树

### Kruskal

```cpp

```

### Prim

```cpp

```

## 拓扑排序

## 网络流

### 最大流Dinic

```cpp
void add_edge(int u, int v, int w){
    // 正向边权为w
    edge[tot].w = w;
    edge[tot].to = v;
    edge[tot].next = head[u];
    head[u] = tot++;
    // 反向边权为0
    edge[tot].w = 0;
    edge[tot].to = u;
    edge[tot].next = head[v];
    head[v] = tot++;
}

struct Dinic{
    int level[N], S, T;
    void init(int _S, int _T){
        S = _S;
        T = _T;
        tot = 0;
        memset(head, -1, sizeof(head));
    }
    bool bfs(){
        queue<int> que;
        memset(level, -1, sizeof(level));
        level[S] = 0;
        que.push(S);
        while(!que.empty()){
            int u = que.front();
            que.pop();
            for(int i = head[u]; i != -1; i = edge[i].next){
                int v = edge[i].to;
                int w = edge[i].w;
                // level[v] == -1为v点没有访问过
                if(level[v] == -1 && w > 0){
                    level[v] = level[u]+1;
                    que.push(v);
                }
            }
        }
        return level[T] != -1;
    }
    // flow为增广路上最小边权
    int dfs(int u, int flow){
        if(u == T)return flow;
		int fw;
        for(int i = head[u]; i != -1; i = edge[i].next){
            int v = edge[i].to, w = edge[i].w;
            if(!w || level[v] != level[u]+1 || (fw=dfs(v, min(flow, w))) <= 0)
                  continue;
            edge[i].w -= fw;
            edge[i^1].w += fw;
			return fw;
        }
        return 0;
    }
    int maxflow(){
        int flow = 0;
        while(bfs())
          flow += dfs(S, INF);
        return flow;    
    }
}dinic;
```

### 费用流

## 二分图匹配