---
title: "multimodal ai code editing with cursor and chatgpt advanced voice mode"
date: 2025-01-25
tags: chatgpt cursor voice coding
---

Technology mashup experiments using Cursor and advanced voice mode in the ChatGPT desktop app to pair code with two AIs.


# Cursor

[Cursor](https://www.cursor.com/) is a fork of [VS Code](https://code.visualstudio.com/) that includes powerful LLM-based coding assistance. It offers features like smart tab-completion, natural language in-line code edit requests, and a chat-based interface that has access to your entire codebase. All of this is very useful, but what sets it apart in my mind is the presentation of proposed updates as if they are a pull request from an AI colleague. Each one is shown as a clear diff with deletions in red and additions in green and each can be accepted or rejected.  In addition, Cursor has a well developed system for specifying what additional context you want to include alongside your queries using the [`@`](https://docs.cursor.com/context/@-symbols/basic) operator. This can guide Cursor towards specific parts of your codebase or external documentation on the web. This system makes it possible to iteratively develop complex features through cycles of querying, reviewing, and applying edits.

![cursor-diffs]({{ site.url }}{{ site.baseurl }}/assets/images/2025-01-25-cursor-with-openai-advanced-voice/cursor-diffs.png)


# ChatGPT MacOS App

The ChatGPT [Desktop App](https://openai.com/chatgpt/desktop/) has a similar inteface to the [web based UI](https://chatgpt.com/) but offers features that integrate with your operating system. This means you can do things like open a chat box at any time by pressing `Alt + Space` and easily attach screenshots as part of your queries. But, what is really fun about the desktop version is that you can give ChatGPT the ability to [work with apps](https://help.openai.com/en/articles/10119604-work-with-apps-on-macos) on your computer. In particular, it can work with Cursor by installing the [visual studio code extension](https://help.openai.com/en/articles/10128592-how-to-install-the-work-with-apps-visual-studio-code-extension). Combine this with [advanced voice mode](https://help.openai.com/en/articles/8400625-voice-mode-faq) providing real-time voice interaction and you get some interesting multimodal code assistant possibilities.


![gpt-desktop-working-with-cursor]({{ site.url }}{{ site.baseurl }}/assets/images/2025-01-25-cursor-with-openai-advanced-voice/gpt-desktop-working-with-cursor.png)


# Example N-body Solver in Python

As a sample problem I chose writing an [N-body](https://en.wikipedia.org/wiki/N-body_problem) solver in Python. To get started, I opened up Cursor and the ChatGPT app, added Cursor as an app ChatGPT could work with, and activated advanced voice mode by clicking the button with the white background in the bottom right of the image above. At this point, I just had an empty directory so I spoke to ChatGPT in order to explain the task and ask if it could see Cursor,

> [Me] Hey, we're going to write an N-Body solver in Python. Can you see the empty Cursor pane?

The ChatGPT voice replied enthusiastically,

> [GPT] Yes, I can see that the textfield is empty. Let’s get started!.

I hit `Cmnd-L` to open up Cursor's AI Sidebar with the Chat tab pre-selected and typed "Lets write an N-body solver in Python". When Cursor had completed it's first pass in the sidebar, I asked ChatGPT if it could see what Cursor had written. Unfortunately, it could not. Apparently it only has access to the panel showing the file you are editing at the moment. I applied the initial diff into an empty main.py file. This is what it looked like,

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

I followed up with ChatGPT to confirm that it could see the code now that it was open in the editor panel of Cursor. Below is a piece of the transcript that is available in the app after the audio session is over,

![chat-1]({{ site.url }}{{ site.baseurl }}/assets/images/2025-01-25-cursor-with-openai-advanced-voice/chat-1.png)

I wanted to further undetstand how ChatGPT was perceiving the code as it changed so I asked Cursor to add some named variables for the sun, earth, and moon initialization. Cursor showed the diff in the main editing pane. ChatGPT could recognize that the code had been updated but couldn't see the green and red colors of the diff.

I accepted the Cursor changes and then tried something more ambituous. I wanted to brainstorm with ChatGPT for a while before asking Cursor to make another update. Warning, what follows involves some fairly technical talk about numerical solvers, but the point is that you can go on a non coding technical detour with the audio model. The initial version of the code used a numerical integration technique called [Verlet integration](https://en.wikipedia.org/wiki/Verlet_integration). I asked ChatGPT how we can improve the integration accuracy. Initially it suggested using a shorter time step or a higher order integration method like [Runge-Kutta](https://en.wikipedia.org/wiki/Runge%E2%80%93Kutta_methods).

I knew that [symplectic integrators](https://en.wikipedia.org/wiki/Symplectic_integrator) with their long-term energy conservation properties are good for gravity solvers and so I asked ChatGPT if either Verlet or Runge-Kutta were symplectic. It knew that Verlet was and that Runge-Kutta was not. I then told ChatGPT that I wanted an improvement that would still be symplectic. It replied that we could keep the Verlet method or use a [leap frog](https://en.wikipedia.org/wiki/Leapfrog_integration) method. Having identified a promising approach with the audio mode, I asked Cursor to update the code to use a leap frog integration method. It was able to do this and here is the final version,

![keep it symplectic]({{ site.url }}{{ site.baseurl }}/assets/images/2025-01-25-cursor-with-openai-advanced-voice/keep-it-symplectic.png)


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


Now I wanted to run the code so I opened a terminal in Cursor. I asked ChatGPT if it could see the terminal window but it could not. I then realized that I had not setup a python virtual env for this project. I've been using [uv](https://github.com/astral-sh/uv) more and more so I  asked Cursor to add header comments to the main.py file that [declared its dependencies](https://docs.astral.sh/uv/guides/scripts/#declaring-script-dependencies) so that I could run it using `uv run main.py` without worrying about setting up a pyproject.toml file. Cursor failed on the first attempt deciding instead to just add a docstring to the top of the file with usage instructions. Usefull, but not what we wanted. I then added the uv docs as a [custom set of docs](https://docs.cursor.com/context/@-symbols/@-docs#add-custom-docs) for Cursor to index and asked again using `@uv` which is the name I gave to the indexed uv docs.

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

I really like having two modalities (text and speech) being used to deliver different types of information during coding. I wouldn't want to listen to the code suggestions verbatim and I also don't want to read the full description of how the code works. When I can see the code in front of me and have an audio conversation with an AI that explains it as I read, I feel like both modalities are being used effectively. The Cursor diffs felt like reviewing a very fast working remote colleague's pull requests while the audio interaction with ChatGPT felt like a brainstorming session with a very knowledgable colleage looking over my shoulder at the pull requests.


I also like the "brainstorm in audio, request in text" break down. I could see a version of this in which Cursor develops an audio plugin, but it was nice to have a separation of concerns in which I could go on long tangents with the audio model to explore a bunch of possibilities and then focus in on one promising one in text mode with the Cursor model.

It would have been even more effective if ChatGPT had access to the rest of the panels in the Cursor app. This would have allowed discussion about the proposed code before the diff was even created, but that may also happen if OpenAI enhances their "working with apps" product.











