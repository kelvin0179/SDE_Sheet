# Day_1

### 1. Sort an array of 0’s 1’s 2’s without using extra space or sorting algo

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

### 2. Repeat and Missing Number

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

### 3. Merge two sorted Arrays without extra space 

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

### 4. Kadane’s Algorithm 

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
* We iterate inside the indexes , meaning i=c[i].
* We take two type of pointers Fast and Slow

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
