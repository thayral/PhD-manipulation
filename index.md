---
layout: default
title: Learning-based slip detection for adaptive grasp control
---

# Learning-based slip detection for adaptive grasp control in robotic manipulation

**PhD Thesis Project — Théo Ayral**  
CEA (Leti & List) · Université Paris-Saclay

<p style="margin-top:0.5rem;">
  <a href="#publications">Publications</a> ·
  <a href="#code--resources">Code & Resources</a> ·
  <a href="#setup--benches">Setup</a> ·
  <a href="#method">Method</a> ·
  <a href="#results">Results</a> ·
  <a href="#latency">Latency</a>
</p>

---





## CONTEXT: TraceBot Project

Robotic manipulation in critical environements.

TraceBot is an EU project developing robotic systems that can **verify and trace manipulation actions** (“audit trail”) for regulated environments, with a focus on handling sterile medical products. :contentReference[oaicite:1]{index=1}

<div style="max-width:920px; margin:0 auto; padding:0 16px;">

  <div style="width:60%; margin:14px auto 6px auto;">
    <video autoplay loop muted playsinline style="width:100%; height:auto; display:block;">
      <source src="media/tracebot-process.mp4" type="video/mp4">
    </video>

    <div style="height:10px;"></div>

    <img src="media/grippermorphism.png" style="width:100%; height:auto; display:block;" alt="Gripper morphism / project overview">
  </div>

  <!-- Bottom-right logo -->
  <div style="width:60%; margin:0 auto;">
    <div style="text-align:right;">
      <a href="https://www.tracebot.eu/">
        <img src="media/TraceBOT_logo.png" style="width:100px; height:auto;" alt="TraceBot logo">
      </a>
    </div>
  </div>

</div>



## TraceBot / manipulation platform
- Multi-finger gripper with **hybrid tactile sensing**
- PzE: high-bandwidth friction-vibration sensing 
- PzR: spatial pressure/contact localization 

<div style="max-width:920px; margin:0 auto; padding:0 16px;">

  <table style="width:100%; border-collapse:collapse;">
    <tr>
      <td width="40%" valign="middle" align="center" style="padding:6px;">
        <video autoplay loop muted playsinline style="width:100%; height:auto; display:block;">
          <source src="media/gripper_demo_2_640.mp4" type="video/mp4">
        </video>
      </td>
      <td width="40%" valign="middle" align="center" style="padding:6px;">
        <video autoplay loop muted playsinline style="width:100%; height:auto; display:block;">
          <source src="media/gripper_demo_3_640.mp4" type="video/mp4">
        </video>
      </td>
    </tr>
    <tr>
      <td width="40%" valign="middle" align="center" style="padding:6px;">
        <video autoplay loop muted playsinline style="width:100%; height:auto; display:block;">
          <source src="media/gripper_demo_6_640.mp4" type="video/mp4">
        </video>
      </td>
      <td width="40%" valign="middle" align="center" style="padding:6px;">
        <video autoplay loop muted playsinline style="width:100%; height:auto; display:block;">
          <source src="media/gripper_demo_10_640.mp4" type="video/mp4">
        </video>
      </td>
    </tr>
  </table>

</div>


<details>
  <summary><strong>Modular, multifingered gripper (hardware · simulation · kinematics)</strong></summary>

  <div style="width:55%; margin:16px auto;">
    <img src="media/three_views_of_gripper.png"
         style="width:100%; height:auto; display:block;"
         alt="Three views of the gripper: hardware photo, digital twin, and kinematic chain">
  </div>

  <div style="width:55%; margin:0 auto; color:#444;">
    <ul>
      <li><strong>Four-fingered, 14-DoF gripper</strong></li>
      <li><strong>Haptic sensing</strong> capabilities</li>
      <li><strong>Hybrid tactile pads</strong> on each phalanx and on the palm</li>
    </ul>
  </div>
</details>



## Hardware platform (overview)



<details>
  <summary><strong>Multifingered gripper & actuation</strong></summary>

<div style="width:70%; margin:16px auto;">
  <img src="media/three_views_of_gripper.png"
       style="height: clamp(120px, 18vh, 180px); width:100%; object-fit:contain; display:block; margin:0 auto;"
       alt="Gripper: hardware, digital twin, kinematic chain">
</div>

<div style="width:70%; margin:16px auto;">
  <img src="media/motor_haptics.png"
       style="height: clamp(120px, 18vh, 180px); width:100%; object-fit:contain; display:block; margin:0 auto;"
       alt="Backdrivable tendon actuation">
</div>


  <div style="width:55%; margin:0 auto; color:#444;">
    <ul>
      <li><strong>Four-fingered, 14-DoF gripper</strong></li>
      <li><strong>Backdrivable tendon actuation</strong> enabling haptic interaction</li>
      <li>Designed for multi-contact grasping and internal force control</li>
    </ul>
  </div>


<!-- Vimeo embed (works on GitHub Pages) -->
<div style="max-width:900px;">
  <div style="position:relative; padding-bottom:56.25%; height:0; overflow:hidden;">
    <iframe
      src="https://player.vimeo.com/video/1056472782?h=0&title=0&byline=0&portrait=0"
      style="position:absolute; top:0; left:0; width:40%; height:40%;"
      frameborder="0"
      allow="autoplay; fullscreen; picture-in-picture"
      allowfullscreen>
    </iframe>
  </div>
</div>



<div style="width:70%; margin:16px auto;">
  <div style="position:relative; padding-bottom:56.25%; height:0; overflow:hidden;">
    <iframe
      src="https://player.vimeo.com/video/1056472782?h=0&title=0&byline=0&portrait=0"
      style="position:absolute; top:0; left:0; width:100%; height:100%;"
      frameborder="0"
      allow="autoplay; fullscreen; picture-in-picture"
      allowfullscreen>
    </iframe>
  </div>
</div>


</details>



<details>
  <summary><strong>Piezoelectric tactile sensing (PzE) — slip detection</strong></summary>

<table style="width:70%; margin:16px auto; border-collapse:collapse;">
  <tr>
    <td width="50%" align="center" valign="middle" style="padding:6px;">
      <img src="media/pze_structure.PNG"
           style="height: clamp(110px, 14vh, 160px); width:100%; object-fit:contain; display:block;"
           alt="Piezoelectric tactile sensor structure">
    </td>
    <td width="50%" align="center" valign="middle" style="padding:6px;">
      <img src="media/pze_signal.png"
           style="height: clamp(110px, 14vh, 160px); width:100%; object-fit:contain; display:block;"
           alt="PzE vibration signal">
    </td>
  </tr>
</table>

  <div style="width:55%; margin:0 auto; color:#444;">
    <p>
      Piezoelectric sensors capture high-frequency dynamics,
      enabling detection of dynamic tactile events and incipient slip.
    </p>
    <p>
      → <a href="https://github.com/thayral/tactile-slip-detection-pze">
        Learn more about tactile slip detection
      </a>
    </p>
  </div>
</details>


<details>
  <summary><strong>Piezoresistive tactile arrays (PzR) — contact estimation</strong></summary>

<table style="width:70%; margin:16px auto; border-collapse:collapse;">
  <tr>
    <td width="50%" align="center" valign="middle" style="padding:6px;">
      <img src="media/pzr_structure.png"
           style="height: clamp(110px, 14vh, 160px); width:100%; object-fit:contain; display:block;"
           alt="Piezoresistive array structure">
    </td>
    <td width="50%" align="center" valign="middle" style="padding:6px;">
      <img src="media/pzr_signal.png"
           style="height: clamp(110px, 14vh, 160px); width:100%; object-fit:contain; display:block;"
           alt="PzR pressure/contact visualization">
    </td>
  </tr>
</table>

  <div style="width:55%; margin:0 auto; color:#444;">
    <p>
      Piezoresistive arrays provide pressure distribution and contact localization,
      supporting grasp modeling and internal force coordination.
    </p>
    <p>
      → <a href="https://github.com/thayral/reactive-slip-control">
        See how PzR sensing is used in reactive slip control
      </a>
    </p>
  </div>
</details>



## Contributions

### C1 — Early slip detection from tactile vibrations
Detect **incipient slip** from high-bandwidth tactile vibrations using learning-based spectro-temporal analysis, operating in **real time (100 Hz)**.

**Related publications**
- **AIM 2023 (published)**  
  *Spectro-Temporal Recurrent Neural Network for Robotic Slip Detection with Piezoelectric Tactile Sensor*  
  Théo Ayral, Saifeddine Aloui, Mathieu Grossard

- *(in preparation)*  
  *Robust Tactile Slip Detection under Manipulation Perturbations*  
  Théo Ayral, Saifeddine Aloui, Mathieu Grossard

**Project page**
- → Tactile slip detection:  
  https://github.com/thayral/tactile-slip-detection-pze


---

### C2 — Data-driven robustness to manipulation perturbations
Improve robustness to transient events and actuation noise through **perturbation-aware training** and haptic data fusion, reducing false alarms while preserving low detection latency.

- Robustness: **38.77 % → 90.43 %**
- Average detection delay: **24.1 ms**
- Perfect recall on slip events

**Related publications**
- *(in preparation)*  
  *Robust Tactile Slip Detection under Manipulation Perturbations*

**Project pages**
- → Tactile slip detection (training & data):  
  https://github.com/thayral/tactile-slip-detection-pze
- → Reactive slip control (use in closed loop):  
  https://github.com/thayral/reactive-slip-control


---

### C3 — Closed-loop adaptation of grasp forces
Stabilize **multi-fingered grasps** by injecting **internal forces** based on tactile feedback, without relying on explicit friction models.

**Related publications**
- **ICRA 2026 (accepted)**  
  *Reactive Slip Control in Multifingered Grasping: Hybrid Tactile Sensing and Internal-Force Optimization*  
  Théo Ayral, Saifeddine Aloui, Mathieu Grossard

- **Patent application (2025)**  
  *Robotic gripper and control method*  
  M. Grossard, S. Aloui, T. Ayral — US Patent Application 19/011,931

**Project page**
- → Reactive slip control:  
  https://github.com/thayral/reactive-slip-control




<table>
  <tr>
    <td>
      <a href="https://thayral.github.io/phd-defense-slides/">
        <img src="media/miniature_beamer.gif" width="420" alt="Slides">
      </a>
    </td>
  </tr>
</table>





## Code & Resources


- **Demo (minimal runnable example):** https://github.com/thayral/<demo-repo>
- **Training / research code (implementation details):** https://github.com/thayral/<training-repo>
- **Slides (full, for deep dive):** https://thayral.github.io/phd-defense-slides/
- **Thesis manuscript (PDF):** https://github.com/thayral/<thesis-repo-or-pdf-link>

## Contact

- Email: 
- Scholar / website: 
- GitHub: https://github.com/thayral
