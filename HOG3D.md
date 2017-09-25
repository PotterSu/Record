HOG3D特征提取步骤：<br>
1.首先计算有多少个cell<br>
2.然后根据cell_size计算每个cell的起始坐标<br>
3.根据step_size计算每个block的起始坐标<br>
4.计算有多少个block<br>
5.按顺序逐个计算每个block的x,y,z坐标，与步骤3不同，这里是将block的index与实际坐标关联<br>
6.初始化x、y、z三个方向的滤波器，分别与CT图像卷积得到三个方向的梯度x_vector、y_vector、z_vector<br>
7.计算三个方向梯度的平方和，x^2+y^2+z^2<br>
8.根据每个体素周围的体素计算它的权重weight<br>
9.建一个四维的向量grad_vector存储x_vector、y_vector、z_vector<br>
10.计算梯度向量与z轴夹角theta,以及x_vector与y_vector之间的夹角phi<br>
11.将180度分到9个方向上每个bin的度数大小t_hist_bins，将360度分到18个方向上每个bin的度数大小p_hist_bins<br>
12.对每个block做如下循环：<br>
{<br>
   &emsp;建一个block的cell数行，9*18个方向列的向量 feature<br>
   &emsp;用full_empty记录以block（i）为起始点一个block尺寸邻域内的体素<br>
   &emsp;如果full_empty总和不为0，执行以下操作:<br>
   &emsp;&emsp;{<br>
      &emsp;&emsp;建一个b_size,b_size,b_size,9,18的向量feature<br>
      &emsp;&emsp;得到某个block的所有体素的weight,magnitudes,theta,phi的信息<br>
      &emsp;&emsp;对该block i的所有体素进行如下操作：<br>
        &emsp;&emsp;&emsp;{<br>
        &emsp;&emsp;&emsp;计算该体素属于第几个cell<br>
        &emsp;&emsp;&emsp;找到当前block(l,m,n)梯度的方向theta和phi，计算角度属于哪个9个和18个中的哪个 bin<br>
        &emsp;&emsp;&emsp;根据我们获得的第几个cell和第几个bin，统计属于该cell的所有magnitudes（l,m,n）*weight(l,m,n)<br>
        &emsp;&emsp;&emsp;}<br>
      &emsp;&emsp;将feature以b_size*b_size*b_size，9，18的方式存储<br>
      &emsp;&emsp;然后将feature进行L2正则化<br>
     &emsp;&emsp; 再将正则后的feature调整成b_size*b_size*b_size,9*18的顺序<br>
   &emsp;}<br>
   &emsp;分别存储block i的feature,block i的起始坐标以及当前block的每个cell的起始坐标<br>
}<br>
