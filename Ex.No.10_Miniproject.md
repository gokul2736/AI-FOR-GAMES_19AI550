# Ex.No: 10 Mini Project
## DATE: 04/09/2026
## REGISTER NUMBER : 212224240086
## Name : Markandeyan Gokul
## AIM:
To develop an advanced 3D Boat Racing game using Unity's Universal Render Pipeline (URP), implementing realistic water physics, player controller mechanics, and AI opponent racing behavior.

## Algorithm:

1. Create a New Unity Project by opening Unity Hub and initializing a 3D URP project, naming it "BoatRacing3D".
2. Import environmental assets (island terrain, water shaders) and 3D boat models into the project.
3. Configure the Universal Render Pipeline (URP) settings and set the Graphics API to Direct3D11 for stable shader compilation.
4. Implement the `PlayerBoatController.cs` script to read user inputs and apply physical forces (throttle and steering) to the boat's Rigidbody.
5. Implement an `AIBoatController.cs` script that calculates the vector towards the next track waypoint and automatically steers the opponent boats.
6. Setup a `WaypointGroup` system to track race checkpoints, ensuring the system safely handles checkpoint indexing to avoid duplicate key errors.
7. Apply the Gerstner Wave algorithm to the water shader for visual wave displacement, and sample these waves for the boat's buoyancy physics.
8. Set up the Main Camera to follow the player, bake the lighting for the island scene, and enter Play mode to test the race mechanics.
## PROGRAM:
#### 1. Player Boat Controller (Player Movement Physics)
```

using UnityEngine;
public class PlayerBoatController : MonoBehaviour
{
    public float motorPower = 5000f;
    public float steerPower = 50f;
    private Rigidbody rb;
    void Start()
    {
        rb = GetComponent<Rigidbody>();
    }
    void FixedUpdate()
    {
        // Get Player Input (W/S for Throttle, A/D for Steering)
        float throttle = Input.GetAxis("Vertical");
        float steer = Input.GetAxis("Horizontal");
        // Apply forward motor force
        Vector3 forwardForce = transform.forward * throttle * motorPower;
        rb.AddForce(forwardForce * Time.fixedDeltaTime, ForceMode.Acceleration);
        // Apply turning rotation
        Quaternion turnRotation = Quaternion.Euler(0f, steer * steerPower * Time.fixedDeltaTime, 0f);
        rb.MoveRotation(rb.rotation * turnRotation);
    }
}

```
#### 2. AI Boat Controller (Opponent Pathfinding)
```

using UnityEngine;
public class AIBoatController : MonoBehaviour
{
    public Transform[] waypoints;
    public float motorPower = 4500f;
    public float turnSpeed = 5f;
    
    private Rigidbody rb;
    private int currentWaypoint = 0;
    void Start()
    {
        rb = GetComponent<Rigidbody>();
    }
    void FixedUpdate()
    {
        if (waypoints.Length == 0) return;
        // Find direction to the next waypoint
        Vector3 targetDirection = waypoints[currentWaypoint].position - transform.position;
        targetDirection.y = 0; 
        
        // Steer towards the waypoint smoothly
        Quaternion targetRotation = Quaternion.LookRotation(targetDirection);
        rb.rotation = Quaternion.Slerp(rb.rotation, targetRotation, turnSpeed * Time.fixedDeltaTime);
        // Always drive forward
        rb.AddForce(transform.forward * motorPower * Time.fixedDeltaTime, ForceMode.Acceleration);
        // Advance to next waypoint if close enough
        if (targetDirection.magnitude < 15f)
        {
            currentWaypoint = (currentWaypoint + 1) % waypoints.Length;
        }
    }
}

```
#### 3. Waypoint Checkpoint System (Safe Registration)
```

using System.Collections.Generic;
using UnityEngine;
public class WaypointGroup : MonoBehaviour
{
    private Dictionary<int, Transform> checkpointPairs = new Dictionary<int, Transform>();
    // Safely registers checkpoints for the race manager without throwing duplicate key crashes
    public void RegisterCheckpoints(Transform[] trackWaypoints)
    {
        for (int i = 0; i < trackWaypoints.Length; i++)
        {
            // Using array indexer instead of .Add() prevents ArgumentException crashes
            checkpointPairs[i] = trackWaypoints[i];
        }
        Debug.Log("Race Track Checkpoints Initialized Successfully.");
    }
}
```
## OUTPUT:
<img width="1920" height="1080" alt="Screenshot (780)" src="https://github.com/user-attachments/assets/6e372bc7-dfb9-4bb5-948b-62e5b5555140" />
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/1bfaa6c7-448c-4c1c-abd6-505016ad58d2" />

## RESULT:
Thus, the 3D Boat Racing game was successfully developed and executed. The project demonstrates the integration of URP graphics, Rigidbody-based player controllers, and waypoint-driven AI pathfinding in a simulated physical environment.
