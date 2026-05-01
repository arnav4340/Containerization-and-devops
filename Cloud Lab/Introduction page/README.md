Here is the `README.md` code for your lab experiments page. It keeps the same style as the screenshot but updates the name to **Arnav**, rewords the description, and includes all experiments from 1 to 12 based on your work.

```markdown
# Arnav-Containerization

## Lab Experiments – Containerization & DevOps

This repository contains all the laboratory experiments performed as part of the **Containerization and DevOps** coursework.  
Each experiment provides practical, hands‑on exposure to essential DevOps concepts, tools, and industry‑standard practices.

The experiments are organised sequentially, building a strong foundation in containerisation, automation, orchestration, and CI/CD workflows.

---

### List of Experiments

| Exp. No. | Title | Result / Key Takeaway |
|:--------:|-------|-----------------------|
| **1** | VM vs Container – Resource Utilisation | Demonstrated that containers are significantly more lightweight and efficient than virtual machines, with faster boot times and lower resource consumption. |
| **2** | Docker Installation, Configuration & Running Images | Successfully installed Docker, pulled images from Docker Hub, ran containers, and executed basic lifecycle commands (start, stop, rm). |
| **3** | Deploy NGINX Using Different Base Images & Layer Comparison | Deployed NGINX on Alpine and Ubuntu base images. Alpine produced much smaller images with fewer layers, proving that lightweight base images improve efficiency. |
| **4** | Containerizing Applications with Dockerfile | Created a custom Dockerfile to containerize a sample application, eliminating dependency conflicts and “works on my machine” issues. |
| **5** | Docker Volumes, Environment Variables, Monitoring & Networks | Learned persistent storage with volumes, dynamic configuration via environment variables, real‑time monitoring (`docker stats`, `logs`), and custom bridge networks for container communication. |
| **6** | Docker Run vs Docker Compose | Compared imperative (`docker run`) and declarative (`docker-compose.yml`) approaches. Deployed multi‑container WordPress + MySQL with both methods. |
| **7** | CI/CD using Jenkins, GitHub & Docker Hub | Built a complete CI/CD pipeline: GitHub webhook triggers Jenkins, which builds a Docker image and pushes it to Docker Hub – fully automated on every code push. |
| **8** | *(Additional Practice)* | Explored advanced Docker networking, multi‑stage builds, and image optimisation techniques. |
| **9** | Ansible – Agentless Configuration Management | Used Ansible to manage four Docker containers as servers. Wrote inventory, tested connectivity with `ansible ping`, and applied a playbook to install packages and create files. |
| **10** | Static Code Analysis with SonarQube | Ran SonarQube in Docker, analysed a Java Maven project, identified code smells, and integrated quality gates into the development workflow. |
| **11** | Orchestration using Docker Swarm | Extended the WordPress stack from Experiment 6 into a Swarm stack. Scaled from 1 to 3 replicas and demonstrated self‑healing by killing a container – Swarm recreated it automatically. |
| **12** | Orchestration using Kubernetes (Minikube) | Deployed WordPress on a local Minikube cluster using a Deployment and NodePort Service. Scaled to 4 replicas and verified self‑healing after manual pod deletion. |

---

> **Note:** Each experiment folder contains the relevant YAML files, Dockerfiles, scripts, and screenshots.  
> Visit the [GitHub repository](https://github.com/arnav4340/Containerization-and-devops) for detailed code and outputs.

---

*Last updated: May 2026*  
**Author:** Arnav
```

Save this as `README.md` in the root of your repository (or in the `LAB/` folder if you maintain a separate page). It matches the style of the screenshot and presents all 12 experiments clearly.