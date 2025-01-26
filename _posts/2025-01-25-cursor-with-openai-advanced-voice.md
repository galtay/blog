---
title: "combining cursor and chatgpt macos app advanced voice mode for pair coding"
date: 2025-01-25
tags: openai cursor voice coding
---

Experiments in having GPT advanced voice mode discuss the code edits created by Cursor.


# Cursor

[Cursor](https://www.cursor.com/) is a fork of
[VS Code](https://code.visualstudio.com/)
that adds a ton of great AI code editor features.
I've copy / pasted my fair share of code snippets into web based LLM UIs to ask about bugs or refactors and have had mostly good results. However, it very quickly becomes tedious to shuffle edits and updates back and forth betweeen the website and the code editor.
Having an AI assitant that "lives in the editor" is a very natural way to remove this step.
It's also an excellent way to automatically leverage context from an entire code base.
Beyond that, Cursor allows you to use the
[`@`](https://docs.cursor.com/context/@-symbols/basic)
symbol to add specific relevant context to questions about your code base.
For the most part, when you ask Cursor to edit or create new code, it presents the updates as diffs on your existing code. Each diff can then be inspected and accepted or rejected. In this way, you can add quite complicated features with multiple rounds of request, review, accept/reject, test.

# ChatGPT MacOS App

The [ChatGPT Desktop App](https://openai.com/chatgpt/desktop/)
is a desktop app that helps integrate ChatGPT into your desktop experience.
You can do things like open a chat box at any time by pressing `Alt + Space`,
easily attach screen shots, and ask for web search to be used when answering your queries.
All of these are doable from the ChatGPT web UI, its just a little more work.
But, what is really fun is that you can give ChatGPT the ability to 
[work with apps](https://help.openai.com/en/articles/10119604-work-with-apps-on-macos)
you have open on your computer.
In particular, it can work with Cursor by installing the
[visual studio code extension](https://help.openai.com/en/articles/10128592-how-to-install-the-work-with-apps-visual-studio-code-extension).
Combine this with [advanced voice mode](https://help.openai.com/en/articles/8400625-voice-mode-faq)
and you have some interesting possibilities.


# Example N-body Solver in Python

To test this out, I opened up Cursor and the ChatGPT app, started a chat with Cursor as an app ChatGPT could work with, and activated advanced voice mode. At this point I just had an empty directory and I spoke to ChatGPT and said "We're going to write an N-body solver in Python, can you see an empty Cursor window?" and the ChatGPT voice replied enthusiastically "Yes, lets get started!".

Next I hit `Cmnd-L` to open up Cursors chat and typed "Lets write an N-body solver in Python".
When Cursor had completed it's first pass in the chat side bar, I asked ChatGPT if it could see the Cursor chat side panel. Unfortunately, it could not. Apparently it only has access to the panel showing the file you are editing at the moment.

Next I applied the initial diff into an empty main.py file. This is what it looked like,

<details>
<summary>

code version 1

</summary>

{% highlight python %}
import numpy as np
import matplotlib.pyplot as plt

class Body:
    G = 6.67430e-11  # gravitational constant

    def __init__(self, mass, position, velocity):
        self.mass = mass
        self.position = np.array(position, dtype=float)
        self.velocity = np.array(velocity, dtype=float)
        self.acceleration = np.zeros(2)

class NBodySimulator:
    def __init__(self, bodies, dt=1.0):
        self.bodies = bodies
        self.dt = dt

    def compute_forces(self):
        # Reset accelerations
        for body in self.bodies:
            body.acceleration = np.zeros(2)

        # Compute gravitational forces between all pairs
        for i, body1 in enumerate(self.bodies):
            for body2 in self.bodies[i + 1:]:
                # Calculate distance vector and magnitude
                r = body2.position - body1.position
                distance = np.linalg.norm(r)

                # Avoid division by zero
                if distance < 1e-10:
                    continue

                # Calculate gravitational force
                force_magnitude = (Body.G * body1.mass * body2.mass) / (distance ** 2)
                force_direction = r / distance

                # Apply forces (F = ma)
                body1.acceleration += force_magnitude * force_direction / body1.mass
                body2.acceleration -= force_magnitude * force_direction / body2.mass

    def update_positions(self):
        # Update velocities and positions using Velocity Verlet integration
        for body in self.bodies:
            body.position += body.velocity * self.dt + 0.5 * body.acceleration * self.dt**2
            old_acceleration = body.acceleration.copy()

            # Compute new forces
            self.compute_forces()

            # Update velocities using average of old and new accelerations
            body.velocity += 0.5 * (old_acceleration + body.acceleration) * self.dt

    def simulate(self, steps):
        positions = []
        for _ in range(steps):
            self.update_positions()
            positions.append([body.position.copy() for body in self.bodies])
        return positions

# Example usage
def main():
    # Create some bodies (mass, position [x,y], velocity [vx,vy])
    bodies = [
        Body(1.989e30, [0, 0], [0, 0]),          # Sun
        Body(5.972e24, [1.496e11, 0], [0, 29.78e3]),  # Earth
        Body(7.348e22, [1.496e11 + 3.844e8, 0], [0, 29.78e3 + 1.022e3])  # Moon
    ]

    # Create simulator
    simulator = NBodySimulator(bodies, dt=3600)  # 1-hour time steps

    # Run simulation for 30 days
    positions = simulator.simulate(steps=30*24)

    # Plot results
    positions = np.array(positions)
    plt.figure(figsize=(10, 10))
    for i in range(len(bodies)):
        plt.plot(positions[:, i, 0], positions[:, i, 1], label=f'Body {i}')
    plt.legend()
    plt.axis('equal')
    plt.show()

if __name__ == "__main__":
    main()
{% endhighlight %}

</details>

<br/>

Next I spoke to ChatGPT to ask what it saw. It replied,
"It looks like you've setup a basic solver using Verlet integration".
I then asked Cursor to add some named variables for the sun, earth, and moon initialization.
Cursor showed the diff in the main editing pane. ChatGPT could recognize that the code had been updated but couldn't see it as a diff. In other words, it could no longer see the code in red that was shown in the Cursor editor as "will be removed".

I then asked GPT "what do you think of this update" in order to check if it had a sense of the previous state of the code or just the current version. It's answer was something along the lines of "This update adding variable names makes the code more readable" so it has some sense of the code varying over time.

I accepted the Cursor changes and then tried something more ambituous. I wanted to brainstorm with ChatGPT for a while before asking Cursor to make another update. The current integration scheme used
[Verlet integration](https://en.wikipedia.org/wiki/Verlet_integration).
I asked ChatGPT how we can improve the integration accuracy.
Initially it suggested using a shorter time step or a higher order integration method like
[Runge-Kutta](https://en.wikipedia.org/wiki/Runge%E2%80%93Kutta_methods).

I knew that [symplectic integrators](https://en.wikipedia.org/wiki/Symplectic_integrator)
with their energy conservation properties are good for gravity solvers and so I asked ChatGPT if either
Verlet or Runge-Kutta were symplectic.
It knew that Verlet was and Runge-Kutta was not. I then told ChatGPT that energy conservation is important
in N-body simulations and asked for an improvement that would still be symplectic.
It replied that we could keep the Verlet method or use a
[leap frog](https://en.wikipedia.org/wiki/Leapfrog_integration)
method.
Having identified a promising approach with the audio mode, I asked Cursor to update the code to use
a leap frog integration method. It was able to do this and here is the final version,


<details>
<summary>

code version 2

</summary>

{% highlight python %}


import numpy as np
import matplotlib.pyplot as plt

class Body:
    G = 6.67430e-11  # gravitational constant

    def __init__(self, mass, position, velocity):
        self.mass = mass
        self.position = np.array(position, dtype=float)
        self.velocity = np.array(velocity, dtype=float)
        self.acceleration = np.zeros(2)

class NBodySimulator:
    def __init__(self, bodies, dt=1.0):
        self.bodies = bodies
        self.dt = dt

    def compute_accelerations(self):
        # Reset accelerations
        for body in self.bodies:
            body.acceleration = np.zeros(2)

        # Compute gravitational forces between all pairs
        for i, body1 in enumerate(self.bodies):
            for body2 in self.bodies[i + 1:]:
                # Calculate distance vector and magnitude
                r = body2.position - body1.position
                distance = np.linalg.norm(r)

                # Avoid division by zero
                if distance < 1e-10:
                    continue

                # Calculate gravitational force
                force_magnitude = (Body.G * body1.mass * body2.mass) / (distance ** 2)
                force_direction = r / distance

                # Apply forces (F = ma)
                body1.acceleration += force_magnitude * force_direction / body1.mass
                body2.acceleration -= force_magnitude * force_direction / body2.mass

    def update_positions(self):
        # Leapfrog integration scheme:
        # 1. Update positions using current velocities (half step)
        for body in self.bodies:
            body.position += 0.5 * self.dt * body.velocity

        # 2. Update accelerations
        self.compute_accelerations()

        # 3. Update velocities using new accelerations
        for body in self.bodies:
            body.velocity += self.dt * body.acceleration

        # 4. Update positions using new velocities (half step)
        for body in self.bodies:
            body.position += 0.5 * self.dt * body.velocity

    def simulate(self, steps):
        positions = []
        # Initial acceleration computation
        self.compute_accelerations()

        for _ in range(steps):
            self.update_positions()
            positions.append([body.position.copy() for body in self.bodies])
        return positions

# Example usage
def main():
    # Masses (kg)
    MASS_SUN = 1.989e30
    MASS_EARTH = 5.972e24
    MASS_MOON = 7.348e22

    # Distances (m)
    EARTH_SEMI_MAJOR_AXIS = 1.496e11  # Earth-Sun distance (AU)
    MOON_SEMI_MAJOR_AXIS = 3.844e8    # Moon-Earth distance

    # Velocities (m/s)
    EARTH_ORBITAL_VELOCITY = 29.78e3
    MOON_ORBITAL_VELOCITY = 1.022e3

    # Create bodies with clear names
    sun = Body(
        mass=MASS_SUN,
        position=[0, 0],
        velocity=[0, 0]
    )

    earth = Body(
        mass=MASS_EARTH,
        position=[EARTH_SEMI_MAJOR_AXIS, 0],
        velocity=[0, EARTH_ORBITAL_VELOCITY]
    )

    moon = Body(
        mass=MASS_MOON,
        position=[EARTH_SEMI_MAJOR_AXIS + MOON_SEMI_MAJOR_AXIS, 0],
        velocity=[0, EARTH_ORBITAL_VELOCITY + MOON_ORBITAL_VELOCITY]
    )

    bodies = [sun, earth, moon]

    # Create simulator
    simulator = NBodySimulator(bodies, dt=3600)  # 1-hour time steps

    # Run simulation for 30 days
    positions = simulator.simulate(steps=30*24)

    # Plot results
    positions = np.array(positions)
    plt.figure(figsize=(10, 10))

    # Update labels to use actual body names
    body_names = ['Sun', 'Earth', 'Moon']
    for i, name in enumerate(body_names):
        plt.plot(positions[:, i, 0], positions[:, i, 1], label=name)

    plt.legend()
    plt.axis('equal')
    plt.title('Solar System Simulation (30 days)')
    plt.xlabel('Position (m)')
    plt.ylabel('Position (m)')
    plt.show()

if __name__ == "__main__":
    main()

{% endhighlight %}

</details>

<br/>


Now I wanted to run the code so I opened a terminal in Cursor. I asked ChatGPT if it could see the terminal window but it replied that it could not. I then realized that I had not setup a python virtual env for this project. I've been using [uv](https://github.com/astral-sh/uv) more and more so I  asked Cursor to add a header to the main.py file that [declared its dependencies](https://docs.astral.sh/uv/guides/scripts/#declaring-script-dependencies)
so that I could run it using `uv run main.py`.
Cursor failed on the first attempt deciding instead to just add a docstring to the top of the file with usage instructions. Usefull, but not what we wanted.
I then added the uv docs as a
[custom set of docs](https://docs.cursor.com/context/@-symbols/@-docs#add-custom-docs)
for Cursor to index and asked again using `@uv` which is the name I gave to the indexed uv docs.

```bash
Try that again by looking at @uv
```

On its second attempt Cursor sucessfully added the following to the top of the file,


```python
# /// script
# dependencies = [
#   "numpy",
#   "matplotlib",
# ]
# requires-python = ">=3.8"
# ///
```

With the above code I was able to run the script and visualize the output with matplotlib using,

```bash
uv run main.py
```

# Take Aways

I really like having two modalities (text and speech) being used to deliver different types of information during coding. I wouldn't want to listen to the code suggestions verbatim and I also don't want to read the full description of how the code works. When I can see the code in front of me and have an audio conversation with an AI that explains it as I read, I feel like both modalities are being used effectively.


I also like the "brainstorm in audio, request in text" break down. I could see a version of this in which Cursor develops an audio plugin, but it was nice to have a separation of concerns in which I could go on long tangents with the audio model to explore a bunch of possibilities and then focus in on one promising one with the Cursor model.

It would have been even more effective if ChatGPT had access to the rest of the panels in the Cursor app. This would have allowed discussion about the proposed code before the diff was even created, but that may also happen if OpenAI enhances their "working with apps" product.











