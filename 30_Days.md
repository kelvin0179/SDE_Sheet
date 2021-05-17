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


