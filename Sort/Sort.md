#排序#

##插入排序##

**sudocode：**

    INSERTION-SORT(A){
      len = A.size();
      
      for(int i=1;i<len;++i){
		key = A[i];
		j=i-1;
		
		while(j>=0 && A[j] > key){
				A[j+1] = A[j];
				j-=1;
		}
		
		A[j+1]=key;
      }
    }

**时间复杂度：**
>考虑最坏情况：待排序数组已逆序排序好。此时上述代码中每一次while都需要执行j+1次。




##归并排序##
**思想：**将两个已排序的子数组归并为一个有序数组

**sudocode：**
>假设子数组 A[p,q]与A[q+1,r]均为已排序的数组。Merge函数将A[p,r]归并为有序数组s

    Merge(A,p,q,r){
		n1=q-p+1;  //子数组A[p,q]的元素个数
		n2=r-q;    //子数组A[q+1,r]的元素个数		
		
		L(n1)={0}; //初始化一个长度为n1的新数组
		R(n2)={0}; //初始化一个长度为n2的新数组
		
		//将子数组A[p,q]复制到L(n1)
		for(i=0 to n1-1){
			L[i]=A[p+i];
		}

		//将子数组A[q+1,r]复制到R(n2)
		for(j=0 to n2-1){
			R[j]=A[q+1+j];
		}

		//Merge
		i=0,j=0;
		for(k=p to r){
			if(L[i]<=R[j]){
				A[k]=L[i];  
				++i;
			}else{
				A[k]=R[j];
				++j;
			}
		}
    }

递归调用

	//待排序数组A[p,r]
    MERT-SORT(A,p,r){
		if(p<r){
			q=[(p+r)/2]
			MERGE-SORT(A,p,q);      //递归至至包含一个元素的子数组时，此时p=0,r=1,q=0.下一次递归p=0=1=0;退出递归开始归并
			MERGE-SORT(A,q+1,r);
			MERGE(A,p,q,r);         //归并
		}
	}

归并排序将数组分解至单个元素数组，至下而上归并排序。


##快速排序##
**思想：**选取某一个参考位置后将两边的数据比照参考位置大小进行**排列**
>这里不是排序，只是按比参考数大或小的排列原则将数据重新排列到两侧

    Partiton(A,p,r){
		x=A[r];
		i=j=p;
		for(j;j<r-1;++j){
			if(A[j]<x){
				i+=1;
				exchange(A[i],A[j])
			}
		}
		exchange(A[i+1],A[r])
	}

    Quick-Sort(A,p,r){
		if(p<r){
			q=Partiton(A,p,r);
			Quick-Sort(A,p,q);
			Quick-Sort(A,q+1,r);
		}
	}

