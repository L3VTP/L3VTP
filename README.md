Table of Contents
=================

   * [Overview]()
   * [ABR algorithm]()
   * [Simlator]()
   * [Dataset]()
   
# Overview of L3VPT
* L3VTP is the first platform of live low latency video platform. 
* L3VTP contains five components: ABR algorithm, live system, simulator, dataset and QoE evaluation. 
* Researches can evaluate their algorithms both on the simulator and the live system. 
* Moreover, we held AITrans 1 , a global ABR competition based on L3VTP. Through the competition experience, we summarize the    pros and cons of the submitted ABR algorithms and verify the fidelity of our simulator. All the resources of L3VTP are open source

# ABR algorithm
* ABR alogorithm is a open modules for reasearch to fill this part.If you are interested it,Please visit the ACM MM Grand challenge[link](https://www.aitrans.online/MMGC/)

# Simlator 
* Code structure
  * Init the env sim :
```python
         net_env = env.Environment(all_cooked_time=all_cooked_time,       # physical time
                                  all_cooked_bw=all_cooked_bw,            # throughput
                                  all_cooked_rtt=all_cooked_rtt,          # rtt
                                 random_seed=random_seed,                 # random_seed
                                  logfile_path=LogFile_Path,              # log setting
                                  VIDEO_SIZE_FILE=video_size_file,        # video trace
                                  Debug = DEBUG)                          # Debug setting
```
  * Loop:
    The cnt control you train steps:
```python    
    if cnt > 50000:
          break;
```
  * every step you can get the observations from env:
```python
        time, time_interval, send_data_size, chunk_len, rebuf, buffer_size, rtt, play_time_len,end_delay, decision_flag, buffer_flag,cdn_flag, end_of_video = net_env.get_video_frame(bit_rate,TARGET_BUFFER[target_buffer])
```
  * calculate the score
    ```python
       # QOE setting 
       if not cdn_flag:
           reward_frame = frame_time_len * float(BIT_RATE[bit_rate]) / 1000  - REBUF_PENALTY * rebuf - LANTENCY_PENALTY  * end_delay
       else:
           reward_frame = -(REBUF_PENALTY * rebuf)
       if decision_flag or end_of_video:
           # reward formate = play_time * BIT_RATE - 1.5 * rebuf - 0.005 * end_delay - 0.02 smooth
           reward_frame -=  * SMOOTH_PENALTY * (abs(BIT_RATE[bit_rate] - BIT_RATE[last_bit_rate]) / 1000)
           
    ```



# Dataset 
* you will have two types of the trace, one is network trace, another is video trace
* Setting 

                   12 TRAIN_TRACES = './train_sim_traces/'   #train trace path setting,
                   13 video_size_file = './video_size_'      #video trace path setting,

* Video trace: Video trace has N files.```(N means the num of bitrate actions)```   
* Video trace format   
   
        |   Time(s)  | frame_data_size(b) |  is_I_flag |
        |------------|--------------------|------------|
        | 22.1131    | 321312             |   1        |  
        
* Network trace Format:   
   
        |Time(s)  | throughput(kpbs) | rtt(ms)|
        |---------|------------------|--------|
        |20.5     | 1.312            |   56   |
        

About livestreaming player sim please click the http://www.aitrans.online


