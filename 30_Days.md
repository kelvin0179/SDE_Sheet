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

### 1. []()
