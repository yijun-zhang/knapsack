# knapsack
new method for unbound knapsack problem

Given a knapsack weight W and a set of n items with certain value val[i] and weight wt[i], pick items into the knapsack (you can pick same item again and again) and get the maximum value.

Please check traditional algorithm

Example:

Input: W=4

         val = [6,18]

        wt = [2,3]

Output : 18

Input : W = 50

         val = [6,18]

        wt =[2,3]

Output: 594

Below is the implementation of  traditional algorithm.

'''
Python3

def unboundedKnapsack(W, val, wt):
    dp = [0 for i in range(W + 1)]
    for i in range(W + 1):
        for j in range(len(wt)):
            if (wt[j] <= i):
                dp[i] = max(dp[i], dp[i - wt[j]] + val[j])

    return dp[W]
'''

Run time is O(nW) , n is the count of items.

If we output the dp list  from 0 to 50 for W=50 val =[6,18] wt = [2,3].

[0, 0, 6, 18, 18, 24, 36, 36, 42, 54, 54, 60, 72, 72, 78, 90, 90, 96, 108, 108, 114, 126, 126, 132, 144, 144, 150, 162, 162, 168, 180, 180, 186, 198, 198, 204, 216, 216, 222, 234, 234, 240, 252, 252, 258, 270, 270, 276, 288, 288, 294]

from i>=3 we find dp[i] = dp[i-3] + 18 .  this is because item[1] has the highest unit value val[1]/wt[1] , we always want to take the item with highest unit value.

Because the item weight is 3 , so if we find the continuous amount of i that satisfied dp[i] = dp[i-3] + 18  greater than 3 , we can break the loop and calculate the result.

algorithm

1: find the item h with highest unit value(w[h],v[h]), it's weight w[h] is the detect window for DP list in step 2

2:while process with DP list , check in range of window w[h] , all DP[i] satisfied with DP[i] = DP[i-w[h]] +v[h] 

   this is mean:

      DP[i] = DP[i-w[h]] +v[h] 

      DP[i+1] = DP[i+1-w[h]] +v[h] 

     .....

     DP[i+w[h]] = DP[i] +v[h] 

3:   once we find the start position i ,that satisfied DP[i] = DP[i-w[h]] +v[h]  in forward window range of w[h], this is mean in this window we always just add item h in position k (i<=k<i+w[h]) based on items in position[k-w[h]] to make the max value .

so for next window for j from i+w[h] to i+w[h]+w[h], we can just add item h at position j based on items in position j - w[h]

then same as followed windows.. 

4: If we reach the W before we get the start position i , we get the result as traditional algorithm.

  otherwise we just get the distance (W-start) from start position i to W , and calculate how many windows(item h add times) and index of based position.

Below is the implementation of  new algorithm.

Python3

def unboundedKnapsackBetter(W, val, wt):
    #get max dense item index
    maxDenseIndex = 0
    #find the item with highest unit value( if two item with same unit value then choose the lighter item)
    for i in range(1,len(val)):
      if Fraction(val[i],wt[i]) > Fraction(val[maxDenseIndex],wt[maxDenseIndex]) \
      or (Fraction(val[i],wt[i]) == Fraction(val[maxDenseIndex],wt[maxDenseIndex]) and wt[i] < wt[maxDenseIndex] ):
        maxDenseIndex = i

    dp = [0 for i in range(W + 1)]

    counter = 0
    breaked = False
    for i in range(W + 1):
        for j in range(len(wt)):
            if (wt[j] <= i):
                dp[i] = max(dp[i], dp[i - wt[j]] + val[j])
        if i-wt[maxDenseIndex] >=0 and dp[i] - dp[i-wt[maxDenseIndex]] == val[maxDenseIndex]:
            counter +=1
            if counter>=wt[maxDenseIndex]:
                breaked = True
                #print(i)
                break
        else:
            counter = 0

    if not breaked:
        return dp[W]
    else:
        start = i - wt[maxDenseIndex] + 1
        times = (W - start) // wt[maxDenseIndex]
        index = (W - start) % wt[maxDenseIndex] + start
        return (times * val[maxDenseIndex] + dp[index])


If we uncomment the #print(i) in the code, we can see i value when we break the loop.

Test input: 

  W = 384

   val = [78, 16, 94 ,36, 87, 93, 50, 22, 63, 28, 91, 60, 64, 27, 41, 27, 73, 37, 12, 69, 68, 30, 83, 31, 63, 24, 68, 36, 30, 3, 23, 59, 70, 68]

   wt = [94 , 57, 12 ,43, 30, 74, 22, 20, 85, 38, 99, 25, 16, 71, 14, 27, 92, 81, 57, 74, 63, 71, 97, 82,  6, 26, 85, 28, 37, 6, 47, 30, 14, 58]

   Output:  4032

the print i value is 11 in this case, that means the run time is (11* 34 ),  while the  traditional algorithm is (W*count of items) = (384*34).
