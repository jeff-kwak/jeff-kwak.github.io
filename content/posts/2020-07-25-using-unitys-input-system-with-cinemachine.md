+++ 
draft = false
date = 2020-07-25T12:21:13-04:00
title = "Using Unity's Input System with Cinemachine"
description = "Unity is in a state of transition. Cinemachine is built to work with the old input system. The new input system causes an error. This is one way to fix it."
slug = "unity-input-system-cinemachine" 
tags = ["unity3d", "cinemachine", "input system", "game-programming"]
categories = []
externalLink = ""
series = []
+++

In Unity3D (v2019.4.x), when you try to use the Cinemachine and the new Input System you will receive a slightly confusing error message:

> InvalidOperationException: You are trying to read Input using the UnityEngine.Input class, but you have switched active Input handling to Input System package in Player Settings.

There's a simple explanation for this. The old Input mechanism is still around, and still supported, and existing packages still have to support it. The solution is to **blank** out the fields `Input Axis Name` in the `Axis Control` for both `Y Axis` and `X Axis`.

![Blank the X Axis and Y Axis Fields](/images/unity-input-system-cinemachine/input-axis-name.png)

This solves the error message, but now the mouse does not move the Cinemachine Freelook Camera. I forget which Unity forum post I found this answer in, so I apologize. You have to add a little script component that maps the `OnLook` actions to the camera's axis (I put it on my player character).

```csharp {linenos=true}
public class ConnectCameraToLook : MonoBehaviour
{
  [Tooltip("A camera following the player's movements")]
  public CinemachineFreeLook FreeLookCamera;

  public void OnLook(InputValue value)
  {
    var inputVector = value.Get<Vector2>();
    FreeLookCamera.m_XAxis.m_InputAxisValue = inputVector.x;
    FreeLookCamera.m_YAxis.m_InputAxisValue = inputVector.y;
  }

```

The key lines are lines 9 and 10 that take some mouse/controller input and map that to the camera axis. This is pretty ugly, and someone from Unity in that same forgotten forum post, promised that support for the new Input System in Cinemachine will be improving :)

Here's a little GitHub project I setup with a third-person character, the new Input System, and a Cinemachine Free Look Camera used as the follow camera. [GitHub: Input Camera and Cinemachine](https://github.com/jeff-kwak/InputManagerAndCinemachine)
