# SDE_Sheet

# Day_1

### 1. [Sort an array of 0’s 1’s 2’s without using extra space or sorting algo](https://leetcode.com/problems/sort-colors/)

* The Idea is to Swap all the 0's to the left.
* Then Swap all the 2's to the right , going from behind.
```cpp
void sortColors(vector<int>& nums) {
        int f=0;
        for(int i=0;i<nums.size();i++)
        {
            if(nums[i]==0)
            {
                swap(nums[i],nums[f]);
                f++;
            }
        }
        f=nums.size()-1;
        for(int i=f;i>=0;i--)
        {
            if(nums[i]==2)
            {
                swap(nums[i],nums[f]);
                f--;
            }
        }
    }
```

### 2. [Repeat and Missing Number](https://practice.geeksforgeeks.org/problems/find-missing-and-repeating/0)

* The Idea is that if a number repeats twice then the XOR of all the elements will cancel out that repeating number.
* We can take that result and XOR it to numbers from (1-n) , Since thats the given range.
* Now we have the result as XOR of the missing element and the repeating element.
* Find the Most Significant Set Bit of that result.
* This set bit because of the XOR of two numbers tells us that one number is carrying that set bit and another does not.
* Then we divide all elements of (1-n) and (c[0]-c[n-1]) into 2 groups according to that position of the set bit.
* Now we have separated both the elements and since we are allowed constant memory , we can do a counter++ for both values.

```cpp
int findDuplicate(vector<int>& c) 
{
    int n=c.size();
    int xorTwo=0;
    for(int i=0;i<n;i++)
    {
        xorTwo^=(i+1);
        xorTwo^=c[i];
    }
    int msb;
    for(int i=29;i>=0;i--)
    {
        if((xorTwo>>i)&1)
            msb=i;
    }
    int leftXor=0,rightXor=0;
    for(int i=0;i<n;i++)
    {
        if(((i+1)>>msb)&1)
            leftXor^=(i+1);
        else
            rightXor^=(i+1);
        if((c[i]>>msb)&1)
            leftXor^=c[i];
        else
            rightXor^=c[i];
    }
    int left=0,right=0;
    for(int i=0;i<n;i++)
    {
        if(leftXor==c[i])
            left++;
        if(rightXor==c[i])
            right++;
    }
    if(left==2)
        return leftXor;
    else
        return rightXor;
}
```

### 3. [Merge two sorted Arrays without extra space](https://practice.geeksforgeeks.org/problems/merge-two-sorted-arrays-1587115620/1)

* The is an Algorithm which is called INDEX-GAP.
* This takes a gap of two-pointer of `ceil(size/2)` and by imagining both the arrays connected we implement on this gap.
* The implementation is that if the element on the left is bigger than the right then we swap.
* After one full iteration we decrease the gap size by `ceil(gap/2)`.
* We do this until size of gap = 1.
* After all this steps the two are sorted.

```cpp
void merge(long long c1[], long long c2[], int n, int m) 
{ 
    int sz1=n+m;
    int sz=sz1;
    while(sz>1)
    {
        int mid=(sz/2);
        if(sz&1)
            mid++;
        
        int l=0,r=mid;
        while(r<sz1)
        {
            int templ,tempr;
            bool f1,f2;
            if(l<n)
                templ=l,f1=0;
            else
                templ=l-n,f1=1;
            if(r<n)
                tempr=r,f2=0;
            else
                tempr=r-n,f2=1;
            if(!f1 and !f2)
            {
                if(c1[templ]>c1[tempr])
                    swap(c1[templ],c1[tempr]);
            }
            if(!f1 and f2)
            {
                if(c1[templ]>c2[tempr])
                    swap(c1[templ],c2[tempr]);
            }
            if(f1 and !f2)
            {
                if(c2[templ]>c1[tempr])
                    swap(c2[templ],c1[tempr]);
            }
            if(f1 and f2)
            {
                if(c2[templ]>c2[tempr])
                    swap(c2[templ],c2[tempr]);
            }
            l++,r++;
        }
        sz=mid;
    }
}
```

### 4. [Kadane’s Algorithm](https://leetcode.com/problems/maximum-subarray/) 

* This is very basic DP where we take the sum of the array.
* While doing so if the sum of the current index is less than the value at the current index , we take the array value as sum at that index.

```cpp
int maxSubArray(vector<int>& c)
{
    int n=c.size();
    vector<int> sum(n,0);
    sum[0]=c[0];
    int ma=c[0];
    for(int i=1;i<n;i++)
    {
        if(sum[i-1]+c[i]>c[i])
            sum[i]=sum[i-1]+c[i];
        else
            sum[i]=c[i];
        ma=max(ma,sum[i]);
    }
    if(ma==0)
    {   ma=c[0];
        for(int i=1;i<n;i++)
        {
            ma=max(ma,c[i]);
        }
    }
    return ma;
}
```

### 5. [Merge Overlapping Subintervals](https://leetcode.com/problems/merge-intervals/)

* This is a very cool Sorting and Searching Problem.
* From an index we can take the right point and check how many left points after that index comes before it.
* Then for all those points we can just take out the maximum right point , then we have a connected interval.
* Sort The array first and the for finding points use Binary Search.

```cpp
vector<vector<int>> merge(vector<vector<int>>& c) 
{
    int n=c.size();
    vector<vector<int>> ans;
    vector<pair<int,int>> p(n);
    for(int i=0;i<n;i++)
    {
        p[i].first=c[i][0];
        p[i].second=c[i][1];
    }
    sort(p.begin(),p.end());
    int i=0;
    auto it1=p.begin();
    while(i<n)
    {
        pair<int,int> pa={p[i].second,1e9};
        auto it=upper_bound(p.begin(), p.end(),pa);
        if(it!=it1)
            it--;
        else
        {
            i++;
            continue;
        }
        int j=it-p.begin(),ma=0;
        while(j>=i)
        {
            ma=max(ma,p[j].second);
            j--;
        }
        j=i;
        while(j<=(it-p.begin()))
        {
            p[j].first=p[i].first;
            p[j].second=ma;
            j++;
        }
        i=it-p.begin();
        it1=it;
        it1++;
    }
    for(int i=n-1;i>=1;i--)
    {
        if(p[i].first==p[i-1].first)
            p[i-1].second=p[i].second;
    }
    for(int i=0;i<n;i++)
    {
        vector<int> t(2);
        t[0]=p[i].first;
        t[1]=p[i].second;
        if(ans.empty())
            ans.push_back(t);
        else
        {
            if(ans.back()!=t)
                ans.push_back(t);
        }
    }
    return ans;
}
```

### 6. [Find the duplicate in an array of N+1 integers](https://leetcode.com/problems/find-the-duplicate-number/)

* This one is very tricky because of the constant space constraint.
* We iterate inside the indexes , meaning `i=c[i]`.
* We take two type of pointers Fast and Slow.
* In Fast we do 2 steps forward `i=c[c[i]]` and in Slow we do `i=c[i]`.
* When Both Strike a common point stop.
* Now change the position of Fast to `i=0`and the do one step iteration on both Slow and Fast.
* Again when they strike a common point that index is the answer.

```cpp
int findDuplicate(vector<int>& c) 
{
    int n=c.size();
    int slowPointer=0,fastPointer=0;
    do
    {
        slowPointer=c[slowPointer];
        fastPointer=c[fastPointer];
        fastPointer=c[fastPointer];
    } while (slowPointer!=fastPointer);
    fastPointer=0;
    do
    {
        slowPointer=c[slowPointer];
        fastPointer=c[fastPointer];
    } while (slowPointer!=fastPointer);
    return slowPointer;
}
```

# Day_2

### 1. [Set Matrix Zeros](https://leetcode.com/problems/set-matrix-zeroes/) 

* We can create two dummy array one row wise and one column wise inside the matrix.
* Before that for the decision of index 0,0 we should precalculate that , does the array we are considering dummy have zeros or not. 
* Traverse from index 1,1 and on encounter with zero keep marking the dummy array as zero at the index which is required.
* Then Traverse again from 1,1 and keep making zero according to the index of the dummy array.

```cpp
void setZeroes(vector<vector<int>>& c) 
{
    bool col=false,row=false;
    int n=c.size();
    int m=c[0].size();
    for(int i=0;i<n;i++)
    {
        if(c[i][0]==0)
            row=true;
    }
    for(int i=0;i<m;i++)
    {
        if(c[0][i]==0)
            col=true;
    }
    for(int i=1;i<n;i++)
    {
        for(int j=1;j<m;j++)
        {
            if(c[i][j]==0)
            {
                c[i][0]=0;
                c[0][j]=0;
            }
        }
    }
    for(int i=1;i<n;i++)
    {
        for(int j=1;j<m;j++)
        {
            if(c[i][0]==0 or c[0][j]==0)
                c[i][j]=0;
        }
    }
    for(int i=0;i<n;i++)
    {
        if(row)
            c[i][0]=0;
    }
    for(int i=0;i<m;i++)
    {
        if(col)
            c[0][i]=0;
    }
}
```

### 2. [Pascal Triangle](https://leetcode.com/problems/pascals-triangle/)

* This is based on a simple DP solution which is shown in the diagram of the question.

```cpp
vector<vector<int>> generate(int n) 
{
    vector<vector<int>> ans;
    ans.push_back({1});
    vector<int> t;
    for(int i=1;i<n;i++)
    {
        t.push_back(1);
        for(int j=0;j<ans[i-1].size()-1;j++)
        {
            t.push_back(ans[i-1][j]+ans[i-1][j+1]);
        }
        t.push_back(1);
        ans.push_back(t);
        t.clear();
    }
    return ans;
}
```

### 3. [Next Permutation](https://leetcode.com/problems/next-permutation/)

* Following the rules of lexographical arrangement we go from behind and check at what point the array becomes decreasing.
* Then we must find the next gretest element from that element in the range of `pointIndex+1 , endPoint`.
* Swap the two elements and sort the same range mentioned above.

```cpp
void nextPermutation(vector<int>& c) 
{
    int n=c.size();
    for(int i=n-1;i>0;i--)
    {
        if(c[i]>c[i-1])
        {
            for(int j=n-1;j>=i;j--)
            {
                if(c[j]>c[i-1])
                {
                    swap(c[j],c[i-1]);
                    break;
                }
            }
            sort(c.begin()+i,c.end());
            return;
        }
    }
    sort(c.begin(), c.end());
}
```

### 4. [Inversion of Array (Using Merge Sort)](https://practice.geeksforgeeks.org/problems/inversion-of-array-1587115620/1)

* The Logic is to observer how we can calculate inversion between two sorted arrays.
* Let's say for every element of the first array we want to find how many elements are there less than in the second array.
* So inversion for each element would be `I = mergedArray.size()-firstArrayCurrentIndex`.
* Using mergeSort we always get to compare sorted arrays in the merge funtion.
* Count the inversion globally and return.

```cpp
vector<long long> left1,right1,merged;
long long int inversions=0;
void merge(long long *c)
{
    int i=0,j=0;
    int n=left1.size();
    int m=right1.size();
    int sz;
    while(i<n or j<m)
    {
        if(j==m)
        {
            sz=merged.size();
            if(!merged.empty())
                inversions+=(sz-i);
            merged.push_back(left1[i]);
            i++;
        }
        else if(i==n)
        {
            merged.push_back(right1[j]);
            j++;
        }
        else
        {
            if(right1[j]<left1[i])
            {
                merged.push_back(right1[j]);
                j++;
            }
            else
            {
                sz=merged.size();
                if(!merged.empty())
                    inversions+=(sz-i);
                merged.push_back(left1[i]);
                i++;
            }
        }
    }
}
void mergeSort(long long *c,int l,int r)
{
    if(l==r)
        return;
    int mid=(l+r)/2;
    mergeSort(c,l,mid);
    mergeSort(c,mid+1,r);
    left1.assign(c+l,c+mid+1);
    right1.assign(c+mid+1,c+r+1);
    merge(c);
    for(int i=r;i>=l;i--)
    {
        c[i]=merged.back();
        merged.pop_back();
    }
    left1.clear();
    right1.clear();
}
long long int inversionCount(long long c[], long long n)
{
    mergeSort(c,0,n-1);
    return inversions;
}
```

### 5. [Stock Buy and Sell](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/)

* The only thing we want is the maximun difference between an element ans any element appearing after it.
* For every iteration we go from back and start by assuming the last element as the max value.
* Then for every upcoming element we store the maximum difference until an element greater than the max value appears.
* In this case we update the maximum and proceed as same.

```cpp
int maxProfit(vector<int>& c)
{
    int n=c.size();
    int i=n-1;
    int diff=0,maxPos=n-1;
    while(i>=0)
    {
        while(i>=0 and c[maxPos]==max(c[i],c[maxPos]))
        {
            diff=max(diff,abs(c[i]-c[maxPos]));
            i--;
        }
        maxPos=i;
    }
    return diff;
}
```
### Another Variation

* Another Variation of this problem is that we can be given the difference between every two elements and be asked to find the same.
* The observation is that every subarray in the array of elements **Not Differences** where the endpoints of the sub array are max and min makes up the entire array.
* Eg (2....5 1....7 3....6).
* So no matter what the elements are between these subarrays , since we know the endpoints are max and min , the sum of differnce of a subarray will be `max-min`.
* As soon as we go from one subarray to another and the fst elt of the second subarray is less the fst elt of first subarray , the sum of difference will go below zero.
* Eg Subarr `1` and `2`.
* And if the other way around then scnd elt of the second subarray will take difference from fst elt of first subarray as the resulting sum of difference.
* So here a **Kadane's Algorithm** will do the trick.

### 6. [Rotate Matrix](https://leetcode.com/problems/rotate-image/)

* This is Medium and the first thought would be a very well implemented recurrsive brute force ,  which I did , but turns out the question was observational

* My Code
```cpp
int n;
void rotation(vector<vector<int>>& c,int pi,int pj,int i,int j,int start,int ending)
{
    if(i==start and j==start)
        return;
    swap(c[i][j],c[pi][pj]);
    if(j==start and i<ending-1)
        rotation(c,i,j,i+1,j,start,ending);
    else if(i==ending-1)
    {
        if(j==ending-1)
            rotation(c,i,j,i-1,j,start,ending);
        else
            rotation(c,i,j,i,j+1,start,ending);
    }
    else if(j==ending-1)
    {
        if(i==start)
            rotation(c,i,j,i,j-1,start,ending);
        else
            rotation(c,i,j,i-1,j,start,ending);
    }
    else
        rotation(c,i,j,i,j-1,start,ending);
}
void rotate(vector<vector<int>>& c)
{
    n=c.size();
    int sz=n;
    int i=1,j=0,up=0,low=n;
    for(int x=0;x<(n/2);x++)
    {
        int sz1=sz-1;
        for(int y=0;y<sz1;y++)
            rotation(c,i-1,j,i,j,up,low);
        up++,low--;
        i++,j++;
        sz-=2;
    }
}
```
* It turns out we just had to reverse the array vertically and swap the diagonal elements.
* Author's Code
```cpp
/*
 * clockwise rotate
 * first reverse up to down, then swap the symmetry 
 * 1 2 3     7 8 9     7 4 1
 * 4 5 6  => 4 5 6  => 8 5 2
 * 7 8 9     1 2 3     9 6 3
*/
void rotate(vector<vector<int> > &matrix) {
    reverse(matrix.begin(), matrix.end());
    for (int i = 0; i < matrix.size(); ++i) {
        for (int j = i + 1; j < matrix[i].size(); ++j)
            swap(matrix[i][j], matrix[j][i]);
    }
}

/*
 * anticlockwise rotate
 * first reverse left to right, then swap the symmetry
 * 1 2 3     3 2 1     3 6 9
 * 4 5 6  => 6 5 4  => 2 5 8
 * 7 8 9     9 8 7     1 4 7
*/
void anti_rotate(vector<vector<int> > &matrix) {
    for (auto vi : matrix) reverse(vi.begin(), vi.end());
    for (int i = 0; i < matrix.size(); ++i) {
        for (int j = i + 1; j < matrix[i].size(); ++j)
            swap(matrix[i][j], matrix[j][i]);
    }
}
```
* The Asymtotic Complexity of both is **O(N^2)** but the author's code is very of very less lines.

# Day_3

### 1. [Search in a 2D matrix](https://leetcode.com/problems/search-a-2d-matrix/)

* The solution is pretty intuitive , as we can assume the whole matrix as a single list and perform Binary Search.
* We just have to manage the indexes when updating left and right.

```cpp
bool searchMatrix(vector<vector<int>>& c, int target)
{
    int n=c.size();
    int m=c[0].size();
    int l=0,r=(m*n)-1;
    while(r-l>1)
    {
        int mid=(l+r)/2;
        if(c[mid/m][mid%m]>=target)
            r=mid;
        else
            l=mid+1;
    }
    if(c[l/m][l%m]==target or c[r/m][r%m]==target)
        return 1;
    return 0;
}
```

### 2. [Pow(x, n)](https://leetcode.com/problems/powx-n/)

* There is a pretty trivial alogorithm which performs **raise to the power** in `log n` time where `n` is the power.
* If the power is negative then just calculate for the positive power and reciprocate it.

```cpp
double power(double x,long long n)
{
    double ans=1;
    while(n>=1)
    {
        ans*=(n%2==0?1:x);
        x=(x*x);
        n/=2;
    }
    return ans;
}
double myPow(double x, int n) 
{
    long long n1=n;
    if(n1>0)
        return power(x,n1);
    else
        return 1/power(x,-n1);
}
```

### 3. [Majority Element](https://leetcode.com/problems/majority-element/)

* There is a very unique algorithm call **Moore's Voting**.
* Here we assume the first element as the max and increment counter on its appearance and decrement when another element is found.
* This idea is when the element is appearing `ceil(n/2)` times then the last element with the positve frequency count will be the answer.
* Since it is guaranted that the answer exists this always works.

```cpp
int majorityElement(vector<int>& c) 
{
    int freq=0,maxEle,i=0;
    while(i<c.size())
    {
        if(freq==0)
            maxEle=c[i];
        if(c[i]==maxEle)
            freq++;
        else
            freq--;
        i++;
    }
    return maxEle;
}
```

### 4. [Majority Element II](https://leetcode.com/problems/majority-element-ii/)

* The same algorithm as above with a slight tweak.
* First observation is that the answer can be no more than 2 elements.
* Let the first two different elements be assigned as maximums and the freqency decrement of these two element does not affect one another.
* Do this till the end and lastly when two elements are found , do a final check in the array and return as a vector.

```cpp
vector<int> majorityElement(vector<int>& c) 
{
    int n=c.size();
    int ct1=0,ct2=0;
    int ele1=-1,ele2=-1;
    int i=0;
    while(i<n)
    {
        if(c[i]==ele1)
            ct1++;
        else if(c[i]==ele2)
            ct2++;
        else if(ct1==0)
        {
            ele1=c[i];
            ct1++;
        }
        else if(ct2==0)
        {
            ele2=c[i];
            ct2++;
        }
        else
            ct1--,ct2--;
        i++;
    }
    ct1=0,ct2=0,i=0;
    while(i<n)
    {
        if(c[i]==ele1)
            ct1++;
        if(c[i]==ele2)
            ct2++;
        i++;
    }
    vector<int> ans;
    if(ct1>(n/3))
        ans.push_back(ele1);
    if(ct2>(n/3))
        ans.push_back(ele2);
    if(ele1==ele2 and ans.size()==2)
        ans.pop_back();
    return ans;
}
```

### 5. [Unique Paths](https://leetcode.com/problems/unique-paths/)

* Because the problem reeks of DP don't go straight for it.
* Upon Observation it can be seen that the different types of moves are fixed.
* What we need to do is take permuataion and take care of the repeated moves.

```cpp
int uniquePaths(int n, int m) 
{
    int down=n-1;
    int rgt=m-1;
    int ma=max(rgt,down);
    int mi=min(down,rgt);
    long ans=1;
    int i;
    for(i=ma+1;i<=(down+rgt);i++)
    {
        if(ans>10000)
            break;
        ans*=i;
    }
    int j=1;
    while(i<=(down+rgt))
    {
        ans*=i;
        ans/=j;
        i++,j++;
    }
    while(j<=mi)
    {
        ans/=j;
        j++;
    }
    return (int)ans;
}
```

### 6. [Reverse Pairs](https://leetcode.com/problems/reverse-pairs/)

* This problem is a slight variation of **Inversion Count** so all the code remains the same just a few lines added.

```cpp
for(i=0;i<m;i++)
        right2.push_back(1ll*right1[i]*2);
    i=0;
    int sz;
    while(i<n or j<m)
    {
        if(j==m)
        {
            sz=merged.size();
            if(!merged.empty())
                inversions+=(sz-i);
            merged.push_back(left1[i]);
            i++;
        }
        else if(i==n)
        {
            merged.push_back(right2[j]);
            j++;
        }
        else
        {
            if(right2[j]<left1[i])
            {
                merged.push_back(right2[j]);
                j++;
            }
            else
            {
                sz=merged.size();
                if(!merged.empty())
                    inversions+=(sz-i);
                merged.push_back(left1[i]);
                i++;
            }
        }
    }
```
# Day_4

### 1. [Two Sum](https://leetcode.com/problems/two-sum/)

* This is very Simple.
* You just have to store the elements before the current index in a map.
* The interative step is just to search the element relatice to target sum inside the map with the current element.

```cpp
vector<int> twoSum(vector<int>& c, int target)
{
    int n=c.size();
    map<int,int> m;
    vector<int> ans(2);
    for(int i=0;i<n;i++)
    {
        if(!m.empty() and m.count(target-c[i]))
        {
            ans[0]=m[target-c[i]];
            ans[1]=i;
            break;
        }
        else
            m[c[i]]=i;
    }
    return ans;
}
```

### 2. [4Sum](https://leetcode.com/problems/4sum/)

* The Idea is of a brute force with a two pointer approach.
* Just do a nested double iteration for the first two numbers and then a two pointer sweep for the remaining.
* Since the array is sorted the two pointer works and we avoid the duplicates.

```cpp
vector<vector<int> > fourSum(vector<int> &c, int k)
{
    int n=c.size();
    sort(c.begin(), c.end());
    vector<vector<int>> ans;
    int i=0,j=1,tempI=-1e8,tempJ=-1e8;
    while(i<n-3)
    {
        while(i<n-3 and tempI==c[i])
            i++;
        if(i==n-3)
            break;
        tempJ=-1e8;
        j=i+1;
        while(j<n-2)
        {
            while(j<n-2 and tempJ==c[j])
                j++;
            if(j==n-2)
                break;
            int l=j+1,r=n-1;
            while(l<r)
            {
                if(c[l]+c[r]+c[i]+c[j]>k)
                    r--;
                else if(c[l]+c[r]+c[i]+c[j]<k)
                    l++;
                else
                {
                    ans.push_back({c[i],c[j],c[l],c[r]});
                    int l1=l,r1=r;
                    while(l<r and c[l]==c[l1] and c[r]==c[r1])
                        l++,r--;
                }
            }
            tempJ=c[j];
            j++;
        }
        tempI=c[i];
        i++;
    }    
    return ans;
}
```
### 3. [Longest Consecutive Sequence](https://leetcode.com/problems/longest-consecutive-sequence/)

* For this problem there are two solutions , one that I wrote and the other is a little simple but with less lines of code.
* For my approach I used a `unordered_map` for **Co-ordinate Compression** and used the compressed number to connect a **DSU**.
* The largest connected component is the final answer.

```cpp
unordered_map<int,int> m;
vector<int> sz,indx;
int numComponents;
int findRoot(int x)
{
    int endPoint=x,tempPoint;
    while(x!=indx[x])
        x=indx[x];
    while(endPoint!=indx[endPoint])
    {
        tempPoint=endPoint;
        endPoint=indx[endPoint];
        indx[tempPoint]=x;
    }
    return x;
}
void union_find(int x,int y)
{
    int find1=findRoot(x),find2=findRoot(y);
    if(find1==find2)
        return;
    // debug2(x,y);
    if(sz[find1]>sz[find2])
    {
        sz[find1]+=sz[find2];
        sz[find2]=1;
        indx[find2]=find1;
    }
    else
    {
        sz[find2]+=sz[find1];
        sz[find1]=1;
        indx[find1]=find2;
    }
    numComponents--;
}
int longestConsecutive(vector<int>& c) 
{
    int n;
    n=c.size();
    numComponents=n;
    for(int i=0;i<n;i++)
    {
        m[c[i]]=i;
        indx.push_back(i);
        sz.push_back(1);
    }
    int key,val;
    for(auto it : m)
    {
        key=it.first;
        val=it.second;
        // debug2(key,val);
        if(m.find(key-1) != m.end())
            union_find(m[key-1],val);
        if(m.find(key+1) != m.end())
            union_find(m[key+1],val);
    }
    int ma=0;
    for(int i=0;i<n;i++)
        ma=max(ma,sz[i]);
    return ma;
}
```
### Another Approach
* Before doing this store everything in the set.
* Another approach is very smart in which we just create an `unordered_set` and for every element we check for `currEle-1` in the set.
* Now when not found that means that element is the smallest of its consecutive sequence.
* So now the task is to search continous consecutive for every element that has no `currEle-1` in it.

### 4. [Largest subarray with 0 sum](https://practice.geeksforgeeks.org/problems/largest-subarray-with-0-sum/1#)

* The Logic is of prefix sum.
* The Two point the prefix sum is same , let's say `(x,y)` then the array size that will contribute to the answer is `y-x`.
* Create the `map` for the elements of the prefix sum and check for repitations and `0`.
* `0` means the whole subarray from start to that point is contributing to the answer.

```cpp
int maxLen(int c[], int n)
{
    map<int,int> m;
    for(int i=1;i<n;i++)
    {
        c[i]+=c[i-1];
    }
    int ma=0;
    for(int i=0;i<n;i++)
    {
        if(c[i]==0)
        {
            ma=max(ma,i+1);
            continue;
        }
        if(m.find(c[i])==m.end())
            m[c[i]]=i;
        else
        {
            ma=max(ma,i-m[c[i]]);
        }
    }
    return ma;
}
```

### 5. [Count the number of subarrays having a given XOR](https://www.geeksforgeeks.org/count-number-subarrays-given-xor/)

* The idea is simply this that if there exists a subarray that that contains the given xor then...
* there will always be a whole subaaray from the starting to that endpoint of the subarray.
* So if we `xor` all the elements of the two subarray then the answer that remains is the subarray starting from the zero position to just before the starting point for the target xor subarray.
* Now we just need to store the `xor` of first two subarrays and store the answer in `map` and then iterate with updating xor to match the value of the third subarray that remains.

```cpp
int countXorSubArray(vector<int>& c,int k)
{
    int n=c.size();
    unordered_map<int,int> m;
    vector<int> temp=c;
    for(int i=1;i<n;i++)
    {
        temp[i]^=temp[i-1];
    }
    for(int i=0;i<n;i++)
    {
        int xorEle=temp[i]^k;
        m[xorEle]=1;
    }
    int ele=0,ct=0;
    for(int i=0;i<n;i++)
    {
        if(m.count(ele))
            ct++;
        ele^=c[i];
    }
    return ct;
}
```

### 6. [Longest Substring Without Repeating Characters](https://leetcode.com/problems/longest-substring-without-repeating-characters/)

* The idea simply is a sliding window whenever we get a repetation of character in the `map`.
* Then we move the second pointer and delete those values(every value that comes) until we remove the repitation.
* Then just take max of the window.
```cpp
int lengthOfLongestSubstring(string c) 
{
    int n=c.size();
    unordered_map<char,int> m;
    for(char i='a';i<='z';i++)
        m[i]=0;
    int ct=0,ans=0;
    int i=0,j=0;
    while(i<n)
    {
        if(m[c[i]]==1)
        {
            while(j<n and c[j]!=c[i])
            {
                m[c[j]]=0;
                j++;
            }
            j++;
            m[c[i]]=0;
        }
        else
        {
            m[c[i]]=1;
            ans=max(ans,i-j+1);
            i++;
        }
    }
    return ans;
}
```
### Another Approach
* It would be just to store the last appearing index of the current element is it is repeated to evade the time taken by the left pointer.
* Then the question is that how do we get rid of those previos elements that appear before the repeated index.
* We don't , if after the removal of the first element if an element exists furthur that appears before the repetation index of the previous element then our its out of our current range , so thats how we discard the repitation and update the index again in the map and move on.

# Day_5

### 1. [Reverse Linked List](https://leetcode.com/problems/reverse-linked-list/)

* The idea is pretty simple , its just to have refernce to the next and the previous node and do the reversal operation.
```cpp
ListNode* reverseList(ListNode* head) 
{
    if(head==NULL)
        return head;
    ListNode *start=head,*curr=head,*left=head,*right=head;
    curr=curr->next;
    right=curr;
    while(curr)
    {
        right=right->next;
        curr->next=left;
        left=curr;
        curr=right;
    }
    head=left;
    start->next=NULL;
    return head;
}
```
### 2. [Middle of the Linked List](https://leetcode.com/problems/middle-of-the-linked-list/)

* This can be done in a naive way , but the optimal solution is a smart one.
* In this approach we take the help of a **Fast Pointer** and a **Slow Pointer**.
* Fast moves two steps at a time and slow with one at a time.
* With some edge case management we can see if the fast hits the last node , we get the middle at the slow pointer.

```cpp
ListNode* middleNode(ListNode* head) 
{
    ListNode *slow = head, *fast = head;
    while (fast && fast->next)
        slow = slow->next, fast = fast->next->next;
    return slow;
}
```

### 3. [Merge Two Sorted Lists](https://leetcode.com/problems/merge-two-sorted-lists/)

* The idea is to take the head pointer as the minimum of the two linked lists and continue with a two pointer of connecting nodes.

```cpp
Node *merge(Node *head1,Node * head2)
{
    Node *head,*start;
    if(head1->data<=head2->data)
    {
        head=head1;
        head1=head1->bottom;
    }
    else
    {
        head=head2;
        head2=head2->bottom;
    }
    start=head;
    while(head1 or head2)
    {
        if(head1 and head2)
        {
            if(head1->data<=head2->data)
            {
                head->bottom=head1;
                head1=head1->bottom;
            }
            else
            {
                head->bottom=head2;
                head2=head2->bottom;
            }
        }
        else if(!head1)
        {
            head->bottom=head2;
            head2=head2->bottom;
        }
        else
        {
            head->bottom=head1;
            head1=head1->bottom;
        }
        head=head->bottom;
    }
    return start;
}
```

### 4. [Remove Nth Node From End of List](https://leetcode.com/problems/remove-nth-node-from-end-of-list/)

* The idea is again of a fast and slow pointer.
* By keeping the distance between them as the given postion of deletion , when fast hits the last node , the slow pointer will be at the target node.

```cpp
ListNode* removeNthFromEnd(ListNode* head, int n) 
{
    ListNode *fast,*slow;
    fast=head,slow=head;
    while(n--)
        fast=fast->next;
    if(!fast)
    {
        head=head->next;
        return head;
    }
    while(fast->next)
    {
        fast=fast->next;
        slow=slow->next;
    }
    slow->next=slow->next->next;
    return head;
}
```

### 5. [Delete Node in a Linked List](https://leetcode.com/problems/delete-node-in-a-linked-list/)

* Upon some observation it is impossible to delete the node.
* But it is possible to overwrite values.

```cpp
void deleteNode(ListNode* node) 
{
    while(1)
    {
        node->val=node->next->val;
        if(node->next->next==NULL)
        {
            node->next=NULL;
            break;
        }
        node=node->next;
    }
}
```

### 6. [Add two numbers represented by linked lists](https://practice.geeksforgeeks.org/problems/add-two-numbers-represented-by-linked-lists/1)

* The idea is to do elementary addition with keeping track of the carry.
* At the end after addtion if the carry remains it will be also considered.
```cpp
struct Node* addTwoLists(struct Node* first, struct Node* second)
{
    // code here
    Node *start,*curr,*left,*right;
    start=left=right=curr=first;
    curr=curr->next;
    right=curr;
    while(curr)
    {
        right=right->next;
        curr->next=left;
        left=curr;
        curr=right;
    }
    first=left;
    start->next=NULL;
    
    start=left=right=curr=second;
    curr=curr->next;
    right=curr;
    while(curr)
    {
        right=right->next;
        curr->next=left;
        left=curr;
        curr=right;
    }
    second=left;
    start->next=NULL;
    
    Node* sumHead=NULL;
    int carry=0;
    while(first or second)
    {
        int sum=0;
        if(first)
        {
            sum+=first->data;
            first=first->next;
        }
        if(second)
        {
            sum+=second->data;
            second=second->next;
        }
        sum+=carry;
        carry=0;
        carry=sum/10;
        sum%=10;
        Node* node=new Node(sum);
        if(sumHead==NULL)
            sumHead=node;
        else
        {
            node->next=sumHead;
            sumHead=node;
        }
    }
    if(carry!=0)
    {
        Node* node=new Node(carry);
        node->next=sumHead;
        sumHead=node;
    }
    return sumHead;
}
```

# Day_6

### 1. [Intersection Point in Y Shapped Linked Lists](https://practice.geeksforgeeks.org/problems/intersection-point-in-y-shapped-linked-lists/1)

* There are two methods to this question , one is to delete all the edges of the first linked list so that the intersection will contain null address , which can be easily found by the second linked list.
* The second method is to take the difference in length of both lists and bring the head node to a relative equal level according to their sum.

```cpp
int intersectPoint(Node* head1, Node* head2)
{
    Node* temp;
    while(head1)
    {
        temp=head1;
        head1=head1->next;
        temp->next=NULL;
    }
    while(head2)
    {
        if(head2->next==NULL)
            return head2->data;
        head2=head2->next;
    }
}
```
### 2. [Reverse a Linked List in groups of given size](https://practice.geeksforgeeks.org/problems/reverse-a-linked-list-in-groups-of-given-size/1)

* This problem is implementation heavy and the logic is exactly what's told in the question.
* My advice will be to draw a diagram of 12 nodes with a division of 4 and try to solve it.

```cpp
struct node *reverse (struct node *head, int k)
{ 
        
    node *start,*curr,*left,*right;
    start=curr=left=right=head;
    int i=0;
    while(curr!=NULL)
    {
        node* tempStart=curr;
        left=curr;
        right=right->next;
        curr=curr->next;
        int k1=k;
        k1--;
        while(k1--)
        {
            if(curr==NULL)
                break;
            right=right->next;
            curr->next=left;
            left=curr;
            curr=right;
        }
        if(i==0)
            head=left;
        else
        {
            start->next=left;
            start=tempStart;
        }
        if(curr==NULL)
            tempStart->next=NULL;
        i++;
    }
    return head;
}
```

### 3. [Palindrome Linked List](https://leetcode.com/problems/palindrome-linked-list/)

* The idea is to look for the middle using fast and slow pointers and then reverse the second half of the list.
* Then the checking for pallindrome is a linear iteration.

```cpp
bool isPalindrome(ListNode* head) 
{
    if(!head->next)
        return 1;
    ListNode *slow=head,*fast=head,*start=head;
    while(fast->next and fast->next->next)
    {
        slow=slow->next;
        fast=fast->next->next;
    }
    ListNode *left=slow->next;
    ListNode *right=left->next,*curr=right,*start1=left;
    while(curr)
    {
        right=curr->next;
        curr->next=left;
        left=curr;
        curr=right;
    }
    start1->next=NULL;
    slow->next=NULL;
    start1=left;
    while(start and start1)
    {
        if(start->val != start1->val)
            return 0;
        start=start->next;
        start1=start1->next;
    }
    return 1;
}
```

### 4. [Find the starting point of the Loop of LinkedList](https://leetcode.com/problems/linked-list-cycle-ii/)

* The idea is again the **Tortoise Method** , in which we take a fast and slow pointer and when the two connects.
* We take the one pointer at the starting point keeping the other idle.
* Then we traverse both in the assigned direction and when they meet that's the answer.

```cpp
ListNode *detectCycle(ListNode *head) 
{
    ListNode *slow=head,*fast=head,*start=head;
    while(1)
    {
        if(!fast or !fast->next)
            return NULL;
        slow=slow->next;
        fast=fast->next->next;
        if(slow==fast)
            break;
    }
    slow=start;
    while(1)
    {
        if(slow==fast)
            break;
        fast=fast->next;
        slow=slow->next;
    }
    return slow;
}
```

### 5. [Flattening of a LinkedList](https://practice.geeksforgeeks.org/problems/flattening-a-linked-list/1)

* The idea is to give a recurrsion until the end of the outer list and from there we merge the couple of last lists till the fisrt outer list and empty the recurrsion stack.

```cpp
Node *merge(Node *head1,Node * head2)
{
    Node *head,*start;
    if(head1->data<=head2->data)
    {
        head=head1;
        head1=head1->bottom;
    }
    else
    {
        head=head2;
        head2=head2->bottom;
    }
    start=head;
    while(head1 or head2)
    {
        if(head1 and head2)
        {
            if(head1->data<=head2->data)
            {
                head->bottom=head1;
                head1=head1->bottom;
            }
            else
            {
                head->bottom=head2;
                head2=head2->bottom;
            }
        }
        else if(!head1)
        {
            head->bottom=head2;
            head2=head2->bottom;
        }
        else
        {
            head->bottom=head1;
            head1=head1->bottom;
        }
        head=head->bottom;
    }
    return start;
}
Node *global;
Node *flatten(Node *root)
{
    if(!root->next)
        return root;
    global=flatten(root->next);
    global=merge(root,global);
    return global;
}
```

### 6. [Rotate List](https://leetcode.com/problems/rotate-list/)

* The question is pretty trivial where you are given an infinite number of rotations and we just `%` the rotations with the size of the list to figure out the effective number of rotations.
* The Next step is to see that if we rotate `k` times from the end then that's equal to `size-k` times from the start , Since visiting end will always tak more time.

```cpp
ListNode* rotateRight(ListNode* head, int k) 
{
    if(!head or !head->next or k==0)
        return head;
    int sz=0;
    ListNode *startPoint=head,*endPoint;
    while(startPoint)
    {
        sz++;
        endPoint=startPoint;
        startPoint=startPoint->next;
    }
    k%=sz;
    k=sz-k;
    startPoint=head;
    ListNode *temp;
    while(k--)
    {
        temp=startPoint->next;
        endPoint->next=startPoint;
        startPoint->next=NULL;
        endPoint=startPoint;
        startPoint=temp;
    }
    return startPoint;
}
```
### Another Approach
* One more way would be to create the circular list with an operation and calculate to delete a particular edge to form the answer.

# Day_7

### 1. [Copy List with Random Pointer](https://leetcode.com/problems/copy-list-with-random-pointer/)

* The idea is to add dummy nodes in between the orignal nodes and redefine the connections.
* With the help of the orignal nodes we can assign the `random` pointers to the new nodes.
* Then assgin the `next` address of the new and old nodes to themselves.
* Return the address of the first new node.

```cpp
Node* copyRandomList(Node* head) 
{
    if(!head)
        return NULL;
    Node *start=head;
    while(head)
    {
        Node *node=new Node(head->val);
        node->next=head->next;
        head->next=node;
        head=head->next->next;
    }    
    head=start;
    while(head)
    {
        if(!head->random)
            head->next->random=head->random;
        else
            head->next->random=head->random->next;
        head=head->next->next;
    }
    head=start;
    start=head->next;
    Node *head1=NULL;
    while(head)
    {
        head1=head->next;
        head->next=head->next->next;
        if(!head1->next)
            head1->next=head1->next;
        else
            head1->next=head1->next->next;
        head=head->next;
    }
    return start;
}
```

### 2. [Trapping Rain Water](https://leetcode.com/problems/trapping-rain-water/)

* The idea behind this is that between the left and the right border we always select the one with the minimum value.
* So the intuition starts as taking two pointers as first and last index and the taking the `fromLeft` and `fromRight` maximun for the answer.
* Then comparing the element of the 1st and last position which ever is minimum we take that with the difference with its respected maximum.

```cpp
int trap(vector<int>& c) 
{
    int n=c.size();
    if(n==0)
        return 0;
    int leftMax=c[0],rightMax=c[n-1];
    int l=0,r=n-1,ans=0;
    while(r-l>0)
    {
        if(c[l]<=c[r])
        {
            leftMax=max(c[l],leftMax);
            ans+=(max(0,leftMax-c[l]));
            l++;
        }
        else
        {
            rightMax=max(rightMax,c[r]);
            ans+=(max(0,rightMax-c[r]));
            r--;
        }
    }
    return ans;
}
```
### 3. [Remove Duplicates from Sorted Array](https://leetcode.com/problems/remove-duplicates-from-sorted-array/)

* This is very trivial , we take a point for editing the numbers and the second pointer for finding the new numbers.

```cpp
int removeDuplicates(vector<int>& c)
{
    if(c.empty())
        return 0;
    int i=0,j=0;
    int n=c.size();
    while(j<n)
    {
        if(j<n and c[j]==c[i])
            j++;
        else
        {
            i++;
            c[i]=c[j];
        }
    }
    return i+1;
}   
```

### 4. [Max Consecutive Ones](https://leetcode.com/problems/max-consecutive-ones/)
* Lmao at this point this should not be a problem.

# Day_8

### 1. [N meetings in one room](https://practice.geeksforgeeks.org/problems/n-meetings-in-one-room-1587115620/1#)

* The idea is to find disjoint ranges.
* We want to end a current meeting as soon as possible so that the next meeting can start.
* So we sort all the range according to the end points and the we give preferance to the starting position.
* Now we keep track of the previous ended meeting and iterate for the next disjoint starting point.

```cpp
int maxMeetings(int c1[], int c2[], int n)
{
    vector<pair<int,int>> p;
    for(int i=0;i<n;i++)
        p.push_back({c1[i],c2[i]});
    sort(p.begin(), p.end(),cmp());
    int ct=1,endPoint=p[0].second;
    for(int i=1;i<n;i++)
    {
        if(p[i].first>endPoint)
            ct++,endPoint=p[i].second;
    }
    return ct;
}
```

### 2. [Minimum Platforms](https://practice.geeksforgeeks.org/problems/minimum-platforms-1587115620/1#)

* The idea is to assign `+1` and `-1` to the starting and ending point and sort them.
* Then do a prefix sum and take the maximum accordingly.

```cpp
int findPlatform(int arr[], int dep[], int n)
{
    vector<pair<int,int>> p;
    for(int i=0;i<n;i++)
    {
        p.push_back({arr[i],1});
        p.push_back({dep[i]+1,-1});
    }
    sort(p.begin(), p.end());
    int ans=0,ma=0;
    for(int i=0;i<p.size();i++)
    {
        ans+=p[i].second;
        ma=max(ma,ans);
    }
    return ma;
}
```

### 3. [Fractional Knapsack](https://practice.geeksforgeeks.org/problems/fractional-knapsack-1587115620/1#)

* The idea is to apply unitary method to take the value of a unit weight for a particular index.
* Sort the array according to a comparator following the above rule.
* Now take the final answer accoring to the input weight.

```cpp
class cmp
{
public:
    bool operator()(Item a,Item b)
    {
        double a1=(a.value*1.0)/a.weight;
        double b1=(b.value*1.0)/b.weight;
        return a1>b1;
    }
};
class Solution
{
    public:
    //Function to get the maximum total value in the knapsack.
    double waapas(int &value,int &currWeight,int &maxWeight)
    {
        double ans=(value*1.0*currWeight)/maxWeight;
        return ans;
    }
    double fractionalKnapsack(int W, Item arr[], int n)
    {
        sort(arr,arr+n,cmp());
        double ans=0;
        for(int i=0;i<n;i++)
        {
            if(W==0)
                break;
            if(W>arr[i].weight)
            {
                ans+=arr[i].value;
                W-=arr[i].weight;
            }
            else
            {
                ans+=waapas(arr[i].value,W,arr[i].weight);;
                W=0;
            }
        }
        return ans;
    }
};
```

# Day_9

### 1. [Subset Sums](https://practice.geeksforgeeks.org/problems/subset-sums2234/1)

* The idea is to make two decision for every step , that is either to chose or not not choose the current element.
* At the end when th eindex reaches to `n` , we store the answer.

```cpp
vector<int> ans;
void recc(vector<int> &c,int &n,int i,int sum)
{
    if(i==n)
    {
        ans.push_back(sum);
        return;
    }
    recc(c,n,i+1,sum);
    recc(c,n,i+1,c[i]+sum);
}
vector<int> subsetSums(vector<int> arr, int N)
{
    recc(arr,N,0,0);
    vector<int> a=ans;
    sort(a.begin(),a.end());
    return a;
}
```

### 2. [Subsets II](https://leetcode.com/problems/subsets-ii/)

* Here the concept of BackTracking is used .
* At first we sort the array for the duplicates to occur together.
* Then elimination of the dublicates in the recurrsion in very easy since the array is sorted.

```cpp
vector<vector<int>> ans;
void subsetFind(vector<int> &c,int x,int n,vector<int> allSet)
{
    if(x==n)
        return;
    int rep=-100;
    for(int i=x;i<n;i++)
    {
        if(rep!=c[i])
        {
            allSet.push_back(c[i]);
            ans.push_back(allSet);
            subsetFind(c,i+1,n,allSet);
            allSet.pop_back();
            rep=c[i];
        }
    }
}
vector<vector<int>> subsetsWithDup(vector<int>& c) 
{
    int n=c.size();
    vector<int> t;
    sort(c.begin(), c.end());
    subsetFind(c,0,n,t);
    ans.push_back({});
    return ans;
}
```

### 3. [Combination Sum](https://leetcode.com/problems/combination-sum/)

* The idea is for reaching the target sum with unique combination it is pretty intuitive to sort the array first.
* After this we can , for every element , subtract the multiple of that number in the increasing order of multiplicity.
* For every multiplicity we subtract the result and send the remaining value to the next element.

```cpp
vector<vector<int>> ans;
void combination(vector<int> &c, int x, int sum, vector<int> store)
{
    if(sum==0)
    {
        ans.push_back(store);
        return;
    }
    if(x==-1 or sum<0)
        return;
    int i=0;
    while(sum-(c[x]*i)>=0)
    {
        combination(c,x-1,sum-(c[x]*i),store);
        store.push_back(c[x]);
        i++;
    }
}
vector<vector<int>> combinationSum(vector<int>& c, int target) 
{
    int n=c.size();
    sort(c.begin(),c.end());
    vector<int> t;
    combination(c,n-1,target,t);
    // debug(ans)
    return ans;
}
```

### 4. [Combination Sum II](https://leetcode.com/problems/combination-sum-ii/)

* Sort the array and upon observing we can notice that , if for every recurrsion call we try all the elements that occur after the previous recurrsio call , then after reaching the end we can always get unique answers.

```cpp
vector<vector<int>> ans;
void combination(vector<int> &c, int x,int n, int sum,vector<int> &store)
{
    if(sum==0)
    {
        ans.push_back(store);
        return;
    }
    if(x==n)
        return;
    int rep=-1;
    for(int i=x;i<n;i++)
    {
        if(c[i]>sum)    return;
        if(rep!=c[i])
        {
            store.push_back(c[i]);
            combination(c,i+1,n,sum-c[i],store);
            store.pop_back();
            rep=c[i];
        }
    }
}
vector<vector<int>> combinationSum2(vector<int>& c, int target) 
{
    sort(c.begin(),c.end());
    int n=c.size();
    vector<int> t;
    combination(c,0,n,target,t);
    return ans;
}
```

### 5. [Palindrome Partitioning ](https://leetcode.com/problems/palindrome-partitioning/)

* The idea is to do a two pointer inside the recurrsion , where for every call we check if the two pointers pointing the substring is pallindrome or not.
* If it is the we shift both the pointer just above the end of the current string and call the next recurrsion.
* In the end if we reach the end then we have a answer and we store it.

```cpp
int n,sz;
vector<vector<string>> c;
bool pallin(string &s)
{
    sz=s.size();
    for(int i=0;i<=sz/2;i++)
    {
        if(s[i]!=s[sz-i-1])
            return 0;
    }
    return 1;
}
void recc(int l,int r,string &s,vector<string> &v)
{
    if(l==r and r==n)
    {
        c.push_back(v);
        return;
    }
    if(r==n)
        return;
    string t;
    while(r<n)
    {
        t.push_back(s[r]);
        if(pallin(t))
        {
            v.push_back(t);
            recc(r+1,r+1,s,v);
            v.pop_back();
        }
        r++;
    }
}
vector<vector<string>> partition(string s)
{
    n=s.size();
    vector<string> v;
    recc(0,0,s,v);
    return c;
}
```

### 6. [Permutation Sequence](https://leetcode.com/problems/permutation-sequence/)

* The first approach will be brute force but upon more observation we can see a pattern emerging.
* The idea is to see that for every position we can calculate the number that will occur there by the count of the factorial of the elements till that position.
* After calculating for that position we tranfer the new remainder as the new input to find the number at the next index.

```cpp
set<int> s;
vector<int> fact;
string getPermutation(int n, int k)
{
    k--;
    for(int i=1;i<=n;i++)
        s.insert(i);
    fact=vector<int>(n+1,1);
    for(int i=2;i<=n;i++)
        fact[i]*=fact[i-1]*i;
    string ans="";
    int sz,rem,quo,i;
    while(!s.empty())
    {
        sz=s.size();
        rem=k%(fact[sz-1]);
        quo=k/(fact[sz-1]);
        i=0;
        for(auto it: s)
        {
            if(i==quo)
            {
                ans+=(to_string(it));
                s.erase(it);
                break;
            }
            i++;
        }
        k=rem;
    }
    return ans;
}
```

# Day_10

### 1. [Permutations](https://leetcode.com/problems/permutations/)

* The idea is to pick and mrak number as they appear in the array and upon completing the whole recurrsion tree we can get the possible permutations.

```cpp
int n;
vector<vector<int>> ans;
vector<bool> vis;
void recc(vector<int> &c,vector<bool> &vis,vector<int> &store,int sz)
{
    if(sz==n)
    {
        ans.push_back(store);
        return;
    }
    for(int i=0;i<n;i++)
    {
        if(vis[i]==0)
        {
            vis[i]=1;
            store.push_back(c[i]);
            recc(c,vis,store,sz+1);
            store.pop_back();
            vis[i]=0;
        }
    }
}
vector<vector<int>> permute(vector<int>& c)
{
    n=c.size();
    vis=vector<bool>(n,0);
    vector<int> t;
    recc(c,vis,t,0);
    return ans;
}
```

### 2. [N-Queens](https://leetcode.com/problems/n-queens/)

* The Brute Force in this Problem is optimized a trick of storing the left and right diagonals.
* The left diagonal has a simple formula for storage that is `row+col` and the right one has `(n-1)+(col-row)`.
* The storing for the row and column is easy now all is left are the recurrsion calls.
* For evey index put a Queens if possible and move to the next row , after completing for that call erase that queen and proceed to the next column.

```cpp
vector<bool> visRow,visCol,visUpDiag,visDownDiag;
vector<vector<string>> ans;
bool check(int &n,int &row,int &col)
{
    if(visRow[row] || visCol[col] || visUpDiag[row+col] || visDownDiag[n-1+col-row])
        return 0;
    return 1;
}
void placeQueen(int &n,int row,vector<string> &s)
{
    if(row==n)
    {
        ans.push_back(s);
        return;
    }
    for(int i=0;i<n;i++)
    {
        if(check(n,row,i))
        {
            s[row][i]='Q';
            visRow[row]=1,visCol[i]=1;
            visUpDiag[row+i]=1,visDownDiag[n-1+i-row]=1;
            placeQueen(n,row+1,s);
            visRow[row]=0,visCol[i]=0;
            visUpDiag[row+i]=0,visDownDiag[n-1+i-row]=0;
            s[row][i]='.';
        }
    }
}
vector<vector<string>> solveNQueens(int n)
{
    visRow=visCol=vector<bool>(n,0);
    visUpDiag=visDownDiag=vector<bool>((2*n)-1,0);
    vector<string> s(n,string(n,'.'));
    placeQueen(n,0,s);
    return ans;
}
```

### 3. [Sudoku Solver](https://leetcode.com/problems/sudoku-solver/)

* There are three things that will need to be stored , **row_elements** , **column_elements** and **box_elements**.
* Keeing track of these will take `O(1)` time to check the validity of the current numbers.
* Now just place a valid number and call the next recurrsion.

```cpp
bool visBox[3][3][10],visRow[9][10],visCol[9][10];
bool done=0;
vector<vector<char>> ans;
vector<pair<int,int>> p;
int sz;
void recc(int pos,vector<vector<char>> &s)
{
    if(done)
        return;
    if(pos==sz)
    {
        ans=s;
        done=1;
        return;
    }
    int x,y;
    string t;
    for(int ele=1;ele<=9;ele++)
    {
        x=p[pos].first;
        y=p[pos].second;
        if(visBox[x/3][y/3][ele]==0 and visRow[x][ele]==0 and visCol[y][ele]==0)
        {
            visBox[x/3][y/3][ele]=1;
            visRow[x][ele]=1;
            visCol[y][ele]=1;
            t=to_string(ele);
            s[x][y]=t[0];
            recc(pos+1,s);
            s[x][y]='.';
            visBox[x/3][y/3][ele]=0;
            visRow[x][ele]=0;
            visCol[y][ele]=0;
        }
    }
}
void solveSudoku(vector<vector<char>>& board) 
{
    memset(visBox,0,sizeof(visBox));
    memset(visRow,0,sizeof(visRow));
    memset(visCol,0,sizeof(visCol));
    for(int i=0;i<9;i++)
    {
        for(int j=0;j<9;j++)
        {
            if(board[i][j]!='.')
            {
                int ele=board[i][j]-48;
                visBox[i/3][j/3][ele]=1;
                visRow[i][ele]=1;
                visCol[j][ele]=1;
            }
            else
                p.push_back({i,j});
        }
    }
    sz=p.size();
    recc(0,board);
    board=ans;
}
```

### 4. [M-Coloring Problem](https://practice.geeksforgeeks.org/problems/m-coloring-problem-1587115620/1)

* In this problem the important thing to notice is that , color assigned to a node does not matter as long as its differnt from adjeacent.
* So for every node we traverse through all the colors and assign what comes first , the point where the color does not fit means the answer does not exists.
* This is because of our initial assumtion that order of the color does not matter as long as its different.

```cpp
bool flag=0;
short color[101];
bool check(bool graph[][101],int col,int node)
{
    for(int i=0;i<101;i++)
    {
        if(graph[node][i] and color[i]==col)
            return 0;
    }
    return 1;
}
bool recc(bool graph[][101],int node,int &colorNum,int ct)
{
    if(ct==0)
        return 1;
    for(int j=1;j<=colorNum;j++)
    {
        if(check(graph,j,node))
        {
            color[node]=j;
            if(recc(graph,node+1,colorNum,ct-1))
                return true;
            color[node]=0;
        }
    }
    return 0;
}
bool graphColoring(bool graph[101][101], int m, int V)
{
    memset(color,0,sizeof(color));
    flag=recc(graph,0,m,V);
    return flag;
}
```

### 5. [Rat in a Maze](https://practice.geeksforgeeks.org/problems/rat-in-a-maze-problem/1#)

* The question asks for the different possible moves going in all the 4 directions.
* So , marking the visited as we go and making the co-ordinate unvisted again is the backtracking here.

```cpp
vector<vector<bool>> vis;
vector<string> ans;
bool check(vector<vector<int>> &m,int &n,int x,int y)
{
    if(x>=n or x<0 or y>=n or y<0)
        return 0;
    if(m[x][y]==0)
        return 0;
    return 1;
}
void recc(vector<vector<int>> &m,int &n,int x,int y,string &s)
{
    if(vis[x][y])
        return;
    vis[x][y]=1;
    if(x==n-1 and y==n-1)
    {
        ans.push_back(s);
        vis[x][y]=0;
        return;
    }
    if(check(m,n,x+1,y))
    {
        s.push_back('D');
        recc(m,n,x+1,y,s);
        s.pop_back();
    }
    if(check(m,n,x,y-1))
    {
        s.push_back('L');
        recc(m,n,x,y-1,s);
        s.pop_back();
    }
    if(check(m,n,x,y+1))
    {
        s.push_back('R');
        recc(m,n,x,y+1,s);
        s.pop_back();
    }
    if(check(m,n,x-1,y))
    {
        s.push_back('U');
        recc(m,n,x-1,y,s);
        s.pop_back();
    }
    vis[x][y]=0;
}
vector<string> findPath(vector<vector<int>> &m, int n)
{
    vis=vector<vector<bool>>(n,vector<bool>(n,0));
    string s;
    if(m[0][0]==0)
        return ans;
    recc(m,n,0,0,s);
    return ans;
}
```

### 6. [Word Break](https://leetcode.com/problems/word-break/)

* Upon some observation it can be observed that if we bulid a recurrsion tree , it is found that from the second edge of the 1st node , the words that are breaking are repeated again.
* So we can solve this using `DP`.
* `Brute Force` works by breaking the string in subtring at every point , and if found in the dictionary we split and do the same thing again.
* `Memeoization` of a later substring can be done using the help of the index from where the string starts.

```cpp
unordered_set<string> hashSet;
vector<short> dp;
bool dynamic(int &n,string &s,int pos)
{
    if(pos==n)
        return 1;
    if(dp[pos]!=-1)
        return dp[pos];
    string temp,wordBreak;
    bool flag=0;
    for(int i=pos;i<n;i++)
    {
        temp.push_back(s[i]);
        wordBreak=s.substr(i+1,n-i+1);
        if(hashSet.find(temp)!=hashSet.end())
            flag|=dynamic(n,s,i+1);
    }
    dp[pos]=flag;
    return flag;
}
bool wordBreak(string s, vector<string>& s1)
{
    int n=s1.size();
    int sz=s.size();
    dp=vector<short>(sz,-1);
    for(int i=0;i<n;i++)
        hashSet.insert(s1[i]);
    return dynamic(sz,s,0);
}
```

# Day_11

### 1. Nth Root of a Number

* We can find `x^n` in `log(n)` time.
* But the problem is that we don't know what `x` is.
* To find that we use `Binary Search`.
* If for a given number `x^n` is greater than it , then we reduce the `x` and vice versa.

```cpp
double calculate(double ele,double n)
{
    double ans=1;
    for(int i=0;i<n;i++)
        ans*=ele;
    return ans;
}
void solve()
{
    int ele,n;
    cin>>ele>>n;
    double low=1,high=ele;
    int i=0;
    while(high-low > (1.0/1e6))
    {
        i++;
        double mid=(low+high)/2;
        double val=calculate(mid,n);
        if(val>ele)
            high=mid;
        else
            low=mid;
    }
    cout<<low;
}
```

### 2. Matrix Median

* Since the elements were distinct the only thing was to find the number for which there is an equal distribution of the numbers in the matrix.
* this can be achieved using `Binary Search`.

```cpp
pair<int,int> cal(vector<vector<int>> &c,int &ele,int &n,int &m)
{
    bool flag=0;
    int low=0,high=0;
    for(int i=0;i<n;i++)
    {
        auto it=lower_bound(c[i].begin(),c[i].end(),ele);
        if(*it==ele)
            flag=1;
        low+=(it-c[i].begin());
        high+=(c[i].end()-it);
    }
    if(flag)
        return make_pair(low,high-1);
    else
        return make_pair(low,high);
}
void solve()
{
    int n,m;
    read(n,m);
    vector<vector<int>> c(n,vector<int>(m));
    for(int i=0;i<n;i++)
    {
        for(int j=0;j<m;j++)
            read(c[i][j]);
    }
    int low=1,high=1e9;
    while(high-low>1)
    {
        debug2(low,high)
        int mid=(low+high)/2;
        pair<int,int> val=cal(c,mid,n,m);
        if(val.first<val.second)
            low=mid;
        else if(val.first>val.second)
            high=mid;
        else
            low=high=mid;
    }
    debug(low)
}
```

### 3. Find the element that appears once in sorted array, and rest element appears twice

* The array is already sorted so with `Binary Search` we can find the single element.
* The logic is just to observe the pairity of the index of the repeated elements before and after the single element.

```cpp
void solve()
{
    int n;
    cin>>n;
    vector<int> c;
    int i,num;
    for(i=0;i<n;i++)
    {
        cin>>num;
        c.push_back(num);
    }
    int low=0,high=c.size()-1;
    while(high-low>1)
    {
        int mid=(low+high)/2;
        if(mid&1)
        {
            if(c[mid]==c[mid-1])
                low=mid;
            else
            {
                if(c[mid-1]!=c[mid] and c[mid+1]!=c[mid])
                {
                    cout<<c[mid];
                    return;
                }
                else
                    high=mid;
            }
        }
        else
        {
            if(c[mid]==c[mid+1])
                low=mid;
            else
            {
                if(c[mid-1]!=c[mid] and c[mid+1]!=c[mid])
                {
                    cout<<c[mid];
                    return;
                }
                else
                    high=mid;
            }
        }
    }
    if(low==0)
        cout<<c[low];
    else
        cout<<c[high];
}
```

### 4. [Search in Rotated Sorted Array](https://leetcode.com/problems/search-in-rotated-sorted-array/)

* The Idea of searching the pivot is to compare every `Binary Search` middle value with its first value to find the correct position.
* Then Use some STL trick to find the element.

```cpp
int n;
int check(vector<int> c,int x)
{
    if(n==1)
        return 1;
    if(x==0 and c[x+1]<c[x])
        return 1;
    else if(x==n-1 and c[x]>c[x-1])
        return 1;
    else
    {
        if(x!=0 and x!=n-1 and c[x-1]<c[x] and c[x]>c[x+1])
            return 1;
    }
    return 0;
}
int search(vector<int> nums, int target)
{
    n=nums.size();
    int l=0,r=n-1;
    while(r-l>1)
    {
        int mid=(l+r)/2;
        // debug3(l,r,mid);
        if(check(nums,mid))
        {
            l=r=mid;
            break;
        }
        if(nums[mid]>nums[0])
            l=mid;
        else
            r=mid;
    }
    int pivot;
    // check(nums,0);
    if(check(nums,l))
        pivot=l;
    else
        pivot=r;
    if(target>=nums[0])
    {
        auto it=lower_bound(nums.begin(),nums.begin()+pivot+1,target);
        if(it!=nums.end() and *it==target)
            return (it-nums.begin());
    }
    else
    {
        if(pivot!=n-1)
        {
            auto it=lower_bound(nums.begin()+pivot+1,nums.end(),target);
            if(it!=nums.end() and *it==target)
                return (it-nums.begin());
        }
    }
    return -1;
}
```

### 5. [Median of 2 sorted arrays]()

### 6. [K-th element of two sorted Arrays](https://practice.geeksforgeeks.org/problems/k-th-element-of-two-sorted-array1317/1)

* The idea is to have a range for binary search of `[0,2e9]` and then perform searching.
* For every element we conclude its range of index by checking the number of elements before and after it.
* According to the range we adjust the middle and proceed.

```cpp
pair<long long,long long> cal(int c1[],int c2[],int n,int m,long long mid)
    {
        int low=0,high=0;
        auto it1=lower_bound(c1,c1+n,mid);
        auto it2=upper_bound(c1,c1+n,mid);
        if(it1!=c1+0)
        {
            it1--;
            low+=(it1-c1+1);
        }
        high+=((c1+n)-it2);
        it1=lower_bound(c2,c2+m,mid);
        it2=upper_bound(c2,c2+m,mid);
        if(it1!=c2)
        {
            it1--;
            low+=(it1-c2+1);
        }
        high+=(c2+m-it2);
        return make_pair(low,high);
    }
long long kthElement(int arr1[], int arr2[], int n, int m, int k)
{
    k--;
    long long low=0,high=2e9,mid,total=n+m,lowRange,upRange;
    while(high-low>1)
    {
        mid=(low+high)/2;
        pair<long long,long long> p=cal(arr1,arr2,n,m,mid);
        lowRange=p.first;
        upRange=total-p.second-1;
        if(lowRange<=k and upRange>=k)
            break;
        else if(lowRange>k)
            high=mid;
        else
            low=mid;
    }
    return mid;
}
```

# Day_12(Binary_tree)

### 1. [Inorder Traversal](https://practice.geeksforgeeks.org/problems/inorder-traversal-iterative/0/?fbclid=IwAR2_lL0T84DnciLyzMTQuVTMBOi82nTWNLuXjUgahnrtBgkphKiYk6xcyJU)

* We know to how to the recurrsive traversal.
* For iterative , the steps we need to vizualise is that for every node we go as left as possible and store every node in a stack.
* When we no more can go left , then if there exist a right node then we go to it and repeat the 2nd process , else we return back to the stack.

```cpp
vector<int> inOrder(Node* root)
{
    vector<int> ans;
    stack<Node*> st;
    while(1)
    {
        if(root!=NULL)
        {
            st.push(root);
            root=root->left;
        }
        else
        {
            if(st.empty())
                break;
            ans.push_back(st.top()->data);
            root=st.top();
            st.pop();
            root=root->right;
        }
    }
    return ans;
}
```

### 2. [Preorder traversal](https://practice.geeksforgeeks.org/problems/preorder-traversal-iterative/0/)

* This follows the same steps as `inorder` just the pushing step is a little different.

```cpp
vector<int> preOrder(Node* root)
{
    vector<int> ans;
    stack<Node*> st;
    while(1)
    {
        if(root!=NULL)
        {
            ans.push_back(root->data);
            st.push(root);
            root=root->left;
        }
        else
        {
            if(st.empty())
                break;
            root=st.top();
            st.pop();
            root=root->right;
        }
    }
    return ans;
}
```

### 3. [Postorder Traversal](https://practice.geeksforgeeks.org/problems/postorder-traversal-iterative/0/?fbclid=IwAR0hBdYqYX4QO4D0pil25OqEKVDYB6RCEY_ilup_-n5f5cffgv611Rl9HXY)

* This one is a bit tricky because for every node wee need to handle its left and right to print the daaata in the current node.
* So for the left node we go by the `inorder` push and then after checking the right for `NULL` we return back to the parent node.
* From the parent node we go right and handle the right node , but when the data of the right subtree is generated we see that just right node is printed last.
* So we can check that to finally print the parent node.

```cpp
vector<int> postOrder(Node* root) {
    vector<int> ans;
    vector<Node*> temp; 
    stack<Node*> st;
    while(1)
    {
        if(!root)
        {
            if(st.empty())
                break;
            if(st.top()->right==NULL or (!temp.empty() and st.top()->right==temp.back()))
            {
                ans.push_back(st.top()->data);
                temp.push_back(st.top());
                st.pop();
            }
            else
                root=st.top()->right;
        }
        else
        {
            st.push(root);
            root=root->left;
        }
    }
    return ans;
}
```

### 4. [Symmetric Tree](https://leetcode.com/problems/symmetric-tree/)

* We need two pointers and from them we check the mirror image of the tree.

```cpp
bool flag=1;
void recc(TreeNode *root1,TreeNode *root2)
{
    if(!root1 and !root2)
        return;
    if((!root1 and root2) or (!root2 and root1) or (root1->val!=root2->val))
    {
        flag=0;
        return;
    }
    recc(root1->right,root2->left);
    recc(root1->left,root2->right);
}
bool isSymmetric(TreeNode* root)
{
    recc(root->left,root->right);
    return flag;
}
```

### 5. []()

# Day_13(Dynamic Programming)

### 1. [Maximum Product Subarray](https://leetcode.com/problems/maximum-product-subarray/)

* The idea is to store the minimum and maximum value as we iterate through and reset the values of min and max to 1 when we hit `0`.
* The reason behind this is so simply pass the max forward and if the min is positive it will remain positive , but if negative , it could turn positive if the current element is negetive.
* This can effect the next max and min so that the current max becomes min and the previous min becomes -min~max.

```cpp
int maxProduct(vector<int>& nums)
{
    int n=nums.size();
    vector<int> maxArr(n),minArr(n);
    maxArr[0]=nums[0],minArr[0]=nums[0];
    int ma=nums[0];
    for(int i=1;i<n;i++)
    {
        if(nums[i]==0)
        {
            minArr[i]=1;
            maxArr[i]=1;
            ma=max(ma,nums[i]);
        }
        else
        {
            minArr[i]=min(nums[i],min(minArr[i-1]*nums[i],maxArr[i-1]*nums[i]));
            maxArr[i]=max(nums[i],max(minArr[i-1]*nums[i],maxArr[i-1]*nums[i]));
            ma=max(ma,max(maxArr[i],minArr[i]));
        }
    }
    return ma;
}
```

### 2. [Longest Increasing Subsequence](https://leetcode.com/problems/longest-increasing-subsequence/)

* The `n^2` solution will definatly work but there is also a `nlog(n)` solution which works on the basis of selecting the minimum element for a index of the increasing subarray without reducing the size of it.
* The reason behind this is that replacing the element in between the subarray does not effect its size but it opens up to the possibility of haaving more numbers infront of it , since the replaced element will always be less than the element at that index.

```cpp
int lengthOfLIS(vector<int> &c)
{
    int n=c.size();
    vector<int> dp;
    for(int i=0;i<n;i++)
    {
        auto it=lower_bound(dp.begin(),dp.end(),c[i]);
        if(it==dp.end())
            dp.push_back(c[i]);
        else
            dp[it-dp.begin()]=c[i];
    }
    n=dp.size();
    return n;
}
```

### 3. [Longest Common Subsequence](https://leetcode.com/problems/longest-common-subsequence/)

* This is solved by considering 3 possibilities.
* When both true ++ both , when not , we take 2 remaining possibilites.

```cpp
int n,m;
int dyanmic(string &s1,string &s2,vector<vector<int>> &dp,int i,int j,int &n,int &m)
{
    if(i==n or j==m)
        return 0;
    if(dp[i][j]!=-1)
        return dp[i][j];
    if(s1[i]==s2[j])
        return 1+dyanmic(s1,s2,dp,i+1,j+1,n,m);
    return dp[i][j]=max(dyanmic(s1,s2,dp,i+1,j,n,m),dyanmic(s1,s2,dp,i,j+1,n,m));
}
int longestCommonSubsequence(string s1, string s2)
{
    n=s1.size();
    m=s2.size();
    vector<vector<int>> dp(n,vector<int>(m,-1));
    return dyanmic(s1,s2,dp,0,0,n,m);
}
```

### 4. [Edit Distance](https://leetcode.com/problems/edit-distance/)

* Inserting a character never is good.
* Delete from any when required and if both equal then ++ both.

```cpp
int n,m;
int dynamic(string &s1,string &s2,int i,int j,vector<vector<int>> &dp)
{
    if(j==m)
        return n-i;
    if(i==n)
        return m-j;
    if(dp[i][j]!=-1)
        return dp[i][j];
    if(s1[i]==s2[j])
        return dp[i][j]=dynamic(s1,s2,i+1,j+1,dp);
    return dp[i][j]=1+min(dynamic(s1,s2,i+1,j,dp),min(dynamic(s1,s2,i+1,j+1,dp),dynamic(s1,s2,i,j+1,dp)));
}
int minDistance(string s1, string s2)
{
    n=s1.size();
    m=s2.size();
    vector<vector<int>> dp(n,vector<int>(m,-1));
    return dynamic(s1,s2,0,0,dp);
}
```

### 5. [Maximum sum increasing subsequence](https://practice.geeksforgeeks.org/problems/maximum-sum-increasing-subsequence4749/1#)

* The normal `n^2` solution of finding the largest subsequence will be used , just instead of the size of the subsequence , we will store the value of it.

```cpp
int dynamic(int c[],int dp[],int &n,int x)
{
    if(x==n-1)
        return c[x];
    if(dp[x]!=-1)
        return dp[x];
    dp[x]=c[x];
    for(int i=x+1;i<n;i++)
    {
        if(c[i]>c[x])
            dp[x]=max(dp[x],c[x]+dynamic(c,dp,n,i));
    }
    return dp[x];
}
int maxSumIS(int arr[], int n)  
{  
    int dp[n];
    memset(dp,-1,sizeof(dp));
    int ma=0;
    for(int i=0;i<n;i++)
        ma=max(ma,dynamic(arr,dp,n,i));
    return ma;
}
```

### 6. [Matrix Chain Multiplication](https://practice.geeksforgeeks.org/problems/matrix-chain-multiplication0303/1#)

* The problem asks for minimum number of operations that will be performed.
* So by a formula the logic works `dp[start][end]=dp[start][mid]+dp[mid][end]+(c[start]*c[mid]*c[end])`.

```cpp
int dynamic(int i,int j,vector<vector<int>> &dp,int c[])
{
    if(j-i<=1)
        return 0;
    if(dp[i][j]!=-1)
        return dp[i][j];
    int mi=1e9;
    for(int x=i+1;x<=j-1;x++)
    {
        mi=min(mi,(c[x]*c[i]*c[j])+dynamic(i,x,dp,c)+dynamic(x,j,dp,c));
    }
    return dp[i][j]=mi;
}

int matrixMultiplication(int N, int arr[])
{
    vector<vector<int>> dp(N,vector<int>(N,-1));
    return dynamic(0,N-1,dp,arr);
}
```

### 7. [Maximum path sum in matrix](https://practice.geeksforgeeks.org/problems/path-in-matrix3805/1)

* Pretty Generic path DP problem.

```cpp
bool check(int &i,int &j,int &n)
{
    if(i<0 or j<0 or i==n or j==n)
        return 0;
    return 1;
}

int dynamic(vector<vector<int>> &dp,vector<vector<int>> &c,int i,int j,int &n)
{
    if(!check(i,j,n))
        return 0;
    if(i==n-1)
        return c[i][j];
    if(dp[i][j]!=-1)
        return dp[i][j];
    return dp[i][j]=max(c[i][j]+dynamic(dp,c,i+1,j,n),max(c[i][j]+dynamic(dp,c,i+1,j+1,n),c[i][j]+dynamic(dp,c,i+1,j-1,n)));
}

int maximumPath(int N, vector<vector<int>> Matrix)
{
    vector<vector<int>> dp(N,vector<int>(N,-1));
    int ma=0;
    for(int i=0;i<N;i++)
        ma=max(ma,dynamic(dp,Matrix,0,i,N));
    return ma;
}
```

### 8. [Coin Change](https://leetcode.com/problems/coin-change/)

* Same CSES problem.

```cpp
int coinChange(vector<int>& coins, int amount)
{
    int n=coins.size();
    vector<int> dp(amount+1,1e9);
    dp[amount]=0;
    int indx=0;
    while(indx<n)
    {
        for(int i=amount;i-coins[indx]>=0;i--)
        {
            dp[i-coins[indx]]=min(dp[i-coins[indx]],1+dp[i]);
        }
        indx++;
    }
    if(dp[0]==1e9)
        return -1;
    return dp[0];
}
```

### 9. [Partition Equal Subset Sum](https://leetcode.com/problems/partition-equal-subset-sum/)

* We used DFS on this rather than DP.

```cpp
int sum=0;
bool flag=0;
void dynamic(vector<int> &c,vector<vector<bool>> &dp,int &n,int i,int target)
{
    if(i==n)
        return;
    if(dp[i][target])
        return;
    if(sum-target==target)
        flag=1;
    dynamic(c,dp,n,i+1,target);
    dynamic(c,dp,n,i+1,target+c[i]);
    dp[i][target]=1;
}
bool canPartition(vector<int>& c)
{
    int n=c.size();
    for(int i=0;i<n;i++)
        sum+=c[i];
    vector<vector<bool>> dp(n,vector<bool>(sum+1,0));
    dynamic(c,dp,n,0,0);
    return flag;
}
```

### 10. [Rod Cutting](https://practice.geeksforgeeks.org/problems/rod-cutting0840/1)

* For Every Rod we cut we know its current value from the given array , now we just need to find that of the remaining rod left.
* This can be done using brute force and then storing the value.

```cpp
int dynamic(int x,int price[],vector<int> &dp)
{
    if(x==0)
        return 0;
    if(dp[x-1]!=-1)
        return dp[x-1];
    int ma=0;
    for(int i=1;i<=x;i++)
        ma=max(ma,price[i-1]+dynamic(x-i,price,dp));
    return dp[x-1]=ma;
}

int cutRod(int price[], int n)
{
    vector<int> dp(n,-1);
    return dynamic(n,price,dp);
}
```

### 11. [Maximum Profit in Job Scheduling](https://leetcode.com/problems/maximum-profit-in-job-scheduling/)

* We sort the given input in a structure such that the ending time and the profit is preferenced.
* So for every starting time we search for an ending time before and take the max profit forward.
* Also taking the max profit forword regarless of the mutual exclusive range is also needed.

```cpp
int jobScheduling(vector<int>& startTime, vector<int>& endTime, vector<int>& profit)
{
    vector<vector<int>> c;
    int n=startTime.size();
    for(int i=0;i<n;i++)
    {
        c.push_back({endTime[i],profit[i],startTime[i]});
    }    
    sort(c.begin(),c.end());
    int ma=0,mx=2e9;
    for(int i=0;i<n;i++)
    {
        vector<int> t={c[i][2],mx,0};
        auto it=upper_bound(c.begin(),c.end(),t);
        if(it!=c.begin())
        {
            it--;
            c[i][1]=max(c[i][1],c[i][1]+c[it-c.begin()][1]);
        }
        if(i>0)
            c[i][1]=max(c[i][1],c[i-1][1]);
        ma=max(ma,c[i][1]);
    }
    return ma;
}
```
