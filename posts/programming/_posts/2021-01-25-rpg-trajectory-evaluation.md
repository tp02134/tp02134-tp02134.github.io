# rpg trajectory evaluation 사용법

---

## Preliminaries

- catkin_simple
- rpg_trajectory_evaluation

[uzh-rpg/rpg_trajectory_evaluation](https://github.com/uzh-rpg/rpg_trajectory_evaluation)

- latex 설치

```jsx
sudo apt install texlive-fonts-recommended texlive-fonts-extra
sudo apt install dvipng
```

---

## Single Evaluation

```jsx
rosrun rpg_trajectory_evaluation analyze_trajectory_single.py <VIO_result> --recalculate_errors --png
```

### Example

- VIO_result : rpg_traj/laptop/vins_line/laptop_vins_line_V2_01

---

## Multiple Evaluation

```jsx
rosrun rpg_trajectory_evaluation analyze_trajectories.py line.yaml --output_dir=<evaluation_result> --results_dir=<VIO_results> --platform laptop --odometry_error_per_dataset --plot_trajectories --rmse_table --rmse_boxplot --png
```

- evaluation_result : /home/hyunjun/rpg_traj/laptop/results
- VIO_results : /home/hyunjun/rpg_traj/
- line.yaml in /home/hyunjun/paper_ws/src/rpg_trajectory_evaluation/analyze_trajectories_config

```jsx
Datasets:
  MH_01:
    label: MH01
  MH_02:
    label: MH02
  MH_03:
    label: MH03
  MH_04:
    label: MH04
  MH_05:
    label: MH05
  V1_01:
    label: V101
  V1_02:
    label: V102
  V1_03:
    label: V103
  V2_01:
    label: V201
  V2_02:
    label: V202
  V2_03:
    label: V203
Algorithms:
  vins_mono:
    fn: traj_est
    label: VINS-Mono
  vins_line: 
    fn: traj_est
    label: VINS-Line
  proposed: 
    fn: traj_est
    label: Proposed
RelDistances: []
RelDistancePercentages: []
```

### Folder Configuration

폴더 이름을 반드시 <environment>_<algorithm>_<dataset>의 형태로 사용해야함

- rpg_traj
    - laptop
        - vins_line
            - laptop_vins_line_MH_01
            - laptop_vins_line_MH_02
            - laptop_vins_line_MH_03
            - laptop_vins_line_MH_04
            - laptop_vins_line_MH_05
        - vins_mono
            - laptop_vins_mono_MH_01
            - laptop_vins_mono_MH_02
            - laptop_vins_mono_MH_03
            - laptop_vins_mono_MH_04
            - laptop_vins_mono_MH_05
- Example

[laptop.zip](/assets/zip/2021-01-25/laptop.zip)

[laptop.tar.xz](/assets/zip/2021-01-25/laptop.tar.xz)

---

### VINS-Mono, VINS-Fusion에서 odometry 결과 저장하는 법

vins_estimator/src/utility/visualization.cpp

```cpp
// write result to file
ofstream foutC(VINS_RESULT_PATH, ios::app);
foutC.setf(ios::fixed, ios::floatfield);
foutC.precision(9);
foutC << header.stamp.toSec()/* * 1e9 */<< " ";
foutC.precision(6);
foutC << estimator.Ps[WINDOW_SIZE].x() << " "
      << estimator.Ps[WINDOW_SIZE].y() << " "
      << estimator.Ps[WINDOW_SIZE].z() << " "
      << tmp_Q.x() << " "
      << tmp_Q.y() << " "
      << tmp_Q.z() << " "
      << tmp_Q.w() << endl;
foutC.close();
```

---

## Multiple evaluation plot line style 변경하는 법

rpg_trajectory_evaluation/plot_utils.py 파일 수정

```jsx
def plot_trajectory_top(ax, pos, color, name, alpha=1.0):
    ax.grid(ls='--', color='0.7')
    # pos_0 = pos - pos[0, :]
    if name == 'Proposed':
        color = color+':'
    elif name == 'VINS-Line':
        color = color+'-.'
    elif name == 'VINS-Mono':
        color = color+'--'
    else:
        color = color+'-'
    ax.plot(pos[:, 0], pos[:, 1], color, alpha=alpha, label=name)

def plot_trajectory_side(ax, pos, color, name, alpha=1.0):
    ax.grid(ls='--', color='0.7')
    # pos_0 = pos - pos[0, :]
    if name == 'Proposed':
        color = color+':'
    elif name == 'VINS-Line':
        color = color+'-.'
    elif name == 'VINS-Mono':
        color = color+'--'
    else:
        color = color+'-'
    ax.plot(pos[:, 0], pos[:, 2], color, alpha=alpha, label=name)
```

![V2_02_trajectory_top.png](/assets/img/posts/2020-01-25/V2_02_trajectory_top.png)

오른쪽 위에 해당하는 이름을 name에다가 넣으면 수정 가능!
