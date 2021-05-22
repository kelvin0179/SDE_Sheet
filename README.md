# SDE_Sheet

# Day_1

### 1. [Sort an array of 0’s 1’s 2’s without using extra space or sorting algo](https://leetcode.com/problems/sort-colors/)

* The Idea is to Swap all the 0's to the left.
* Then Swap all the 2's to the right , going from behind.
```
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

```
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

```
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

```
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

```
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

```
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

```
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

```
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

```
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

```
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

```
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
```
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
```
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

```
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

```
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

```
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

```
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

```
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

```
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

```
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

```
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

```
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

```
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

```
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
```
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
```
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

```
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

```
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

```
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

```
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
```
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

```
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

```
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

```
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

```
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

```
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

```
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
