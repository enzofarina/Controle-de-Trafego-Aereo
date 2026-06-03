# Controle de Tráfego Aéreo

Simulation of an air traffic control system written in C. Each aircraft runs as an independent process, communicating with the controller through shared memory and Unix signals.

---

### How it works

The program spawns one process per aircraft. Each aircraft enters the airspace from a random side (east or west), at a random Y coordinate, and moves toward the center (runway) at each cycle. The parent process acts as the controller, monitoring positions and detecting potential collisions every second.

When two aircraft on the same runway get too close, the controller reacts:
- suggests a runway change if an alternative is available
- reduces the speed of the more distant aircraft if no runway is free
- removes the aircraft as a last resort if collision is unavoidable

Coordination is done entirely through `SIGUSR1` (toggle speed) and `SIGUSR2` (change runway), handled by each aircraft's process. State is shared via a POSIX shared memory segment.

---

### Concepts

- Processes and `fork()`
- POSIX shared memory (`shmget`, `shmat`, `shmctl`)
- Unix signals and signal handlers (`signal`, `kill`, `SIGUSR1`, `SIGUSR2`)
- Euclidean distance for proximity detection
- Runway assignment based on entry side and Y coordinate

---

### Runways

| Entry side | Y > 0.5 | Y ≤ 0.5 |
|---|---|---|
| West | 3 | 18 |
| East | 6 | 27 |

---

### Build and run

```bash
gcc controlador_aereo.c -o controlador_aereo -lm
./controlador_aereo <num_aircraft>
```

Example with 4 aircraft:

```bash
./controlador_aereo 4
```

---

### Output example

```
[Aircraft 1 | PID 12345] Delay: 2s
[Aircraft 1 | PID 12345] Entered airspace.
--- Aircraft Status ---
Aircraft 1: | Distance: 0.47 | Coordinates: 0.05, 0.72 | Landed: No | Runway: 3 | Speed: 0.05
...
Possible collision between [Aircraft 1 | PID 12345 | Runway: 3] and [Aircraft 3 | PID 12347 | Runway: 3]
Runway change suggestion: [Aircraft 1 | PID 12345 | Alternative runway: 18]
```

---

### License

MIT
