## Ex.No-8-Implementation-of-Path-finding-using-A-algorithm

### DATE : 15/05/2026
### REGISTER NUMBER : 212223240110

### AIM :
To write a program to create graph using waypoints and use A* algorithm to find path between source and destination.

### Algorithm :
1. Create a New Unity Project by Open the  Unity Hub and create a new 3D Project,Name the project (e.g., Pathfinding).
2. Create Waypoints in Scene => Create empty or sphere GameObjects ( minimum 4)  and  name it as Waypoint1, Waypoint2, ..., Waypoint4
   Position them freely in the scene (not on a grid)
3. Write a waypoint script to draw the lines between neighbors.
4. Attach waypoint.cs script to each waypoint GameObject and Manually assign neighbors in the Inspector to form a graph.
5. Create a empty game object and name it as WaypointManager - to manage all waypoints
6. Attach Waypoint script to it
7. Write a Pathfinding algorithm using A*search
8. Create a Game Object for Player ( choose capsule or any others) and attach the script to move player from start to end waypoints

### Program :
#### Waypoint.cs:
```
using UnityEngine;
using System.Collections.Generic;

public class Waypoint : MonoBehaviour
{
    public List<Waypoint> neighbors = new List<Waypoint>();

    private void OnDrawGizmos()
    {
        Gizmos.color = Color.yellow;
        foreach (var neighbor in neighbors)
        {
            if (neighbor != null)
            {
                Gizmos.DrawLine(transform.position, neighbor.transform.position);
            }
        }
    }
}
```
#### WaypointGraph.cs:
```
using UnityEngine;

public class WaypointGraph : MonoBehaviour
{
    public Waypoint[] allWaypoints;
    void Awake()
    {
        allWaypoints = Object.FindObjectsByType<Waypoint>(FindObjectsSortMode.None);
    }
} 
```
#### Pathfinding.cs:
```
using System.Collections.Generic;
using UnityEngine;

public class Pathfinding : MonoBehaviour
{
    public static List<Waypoint> FindPath(Waypoint start, Waypoint goal)
    {
        var openSet = new List<Waypoint>();
        var cameFrom = new Dictionary<Waypoint, Waypoint>();
        var gScore = new Dictionary<Waypoint, float>();
        var fScore = new Dictionary<Waypoint, float>();

        foreach (var wp in Object.FindObjectsByType<Waypoint>(FindObjectsSortMode.None))
        {
            gScore[wp] = float.PositiveInfinity;
            fScore[wp] = float.PositiveInfinity;
        }

        gScore[start] = 0f;
        fScore[start] = Vector3.Distance(start.transform.position, goal.transform.position);
        openSet.Add(start);

        while (openSet.Count > 0)
        {
            // Find the node in openSet with the lowest fScore
            Waypoint current = openSet[0];
            foreach (var wp in openSet)
            {
                if (fScore[wp] < fScore[current])
                {
                    current = wp;
                }
            }

            // Path found!
            if (current == goal)
            {
                return ReconstructPath(cameFrom, current);
            }

            openSet.Remove(current);

            foreach (var neighbor in current.neighbors)
            {
                float tentativeG = gScore[current] + Vector3.Distance(current.transform.position, neighbor.transform.position);

                if (tentativeG < gScore[neighbor])
                {
                    cameFrom[neighbor] = current;
                    gScore[neighbor] = tentativeG;
                    fScore[neighbor] = tentativeG + Vector3.Distance(neighbor.transform.position, goal.transform.position);

                    if (!openSet.Contains(neighbor))
                    {
                        openSet.Add(neighbor);
                    }
                }
            }
        }
        return null; // No path exists
    }

    private static List<Waypoint> ReconstructPath(Dictionary<Waypoint,Waypoint>cameFrom, Waypoint current)
    {
        var path = new List<Waypoint> { current };
        while (cameFrom.ContainsKey(current))
        {
            current = cameFrom[current];
            path.Insert(0,current);
        }
        return path;
    }
}
```
### AICharacter.cs :
```
using UnityEngine;
using System.Collections.Generic;

public class AICharacter : MonoBehaviour
{
    public Waypoint startWaypoint;
    public Waypoint goalWaypoint;
    public float speed=3f;

    private List<Waypoint> path;
    private int currentIndex = 0;

    void Start()
    {
        if (startWaypoint!=null && goalWaypoint != null)
        {
            path = Pathfinding.FindPath(startWaypoint, goalWaypoint);
        }
    }

    void Update()
    {
        if (path == null || currentIndex >= path.Count) return;

        // Move towards the current waypoint in the list
        Vector3 target = path[currentIndex].transform.position;
        transform.position = Vector3.MoveTowards(transform.position, target, speed * Time.deltaTime);

        // Check if we reached the waypoint
        if (Vector3.Distance(transform.position, target) < 0.1f)
        {
            currentIndex++;
        }
    }
}
```

### Output :
#### Before implementation :
<img width="1918" height="1021" alt="Screenshot 2026-05-15 084347" src="https://github.com/user-attachments/assets/f96e57b4-a0fa-49ae-a764-6d49c5893594" />

#### After implementation :
<img width="1918" height="1030" alt="Screenshot 2026-05-15 084436" src="https://github.com/user-attachments/assets/08ebae9b-31b5-4281-943d-a52bb9a42670" />
<img width="1918" height="1026" alt="Screenshot 2026-05-15 084507" src="https://github.com/user-attachments/assets/833c66a5-e042-493d-a69a-8a02daf40bae" />

### Result :
Thus the pathfinding algorithm was sucessfully implemented.
