---
title: DevPods
description: GPU-accelerated developer pods providing instant, cloud-native development environments with SSH access and dedicated GPU resources.
tags:
  - DevPods
  -   - Developer Environments
      -   - GPU Computing
          -   - SSH Access
              -   - Cloud Development
                  -   - AI Development
                      -   - NVIDIA GPUs
                          - ---

                          GPU-accelerated developer pods that provide instant, isolated development environments with direct SSH access and dedicated GPU resources.

                          ---

                          ## Developer Experience

                          ### Instant Developer Environments
                          Launch fully configured GPU-enabled development containers in minutes. DevPods provide a ready-to-use Linux environment (Ubuntu 24.04) with GPU drivers pre-installed, eliminating complex local setup.

                          ---

                          ### SSH Access
                          Connect directly to your DevPod using standard SSH tools. Buzz HPC auto-generates SSH key pairs and provides a ready-to-use SSH command for immediate access.

                          ---

                          ### Persistent Workspaces
                          DevPods are organized within workspaces, allowing teams to manage multiple isolated environments for different projects, teams, or experiments.

                          ---

                          ## Resource Management

                          ### Flexible GPU Configuration
                          Choose from a range of NVIDIA GPU types (H200, A40, H100) and configure the number of GPUs, CPU cores (in millicores), and memory (in MiB) to match your workload requirements.

                          ---

                          ### Compute Profiles
                          Select from multiple compute profiles tailored for different use cases:
                          - **Serverless profiles** for on-demand, pay-per-use GPU access
                          - - **Dedicated profiles** for reserved, exclusive GPU capacity
                           
                            - ---

                            ## Security

                            ### Auto-Generated SSH Keys
                            Buzz HPC can automatically generate an SSH key pair for your DevPod. The private key is available for download directly from the portal, and the SSH command is pre-configured for immediate use.

                            ---

                            ### Custom SSH Keys
                            Bring your own public SSH key to inject into the DevPod for access with your existing credentials.

                            ---

                            ## Lifecycle Management

                            ### Re-publish
                            DevPods can be re-published to apply configuration changes or recover from a failure state without losing your work.

                            ---

                            ### Status Monitoring
                            Real-time status tracking with clear indicators: Success, Pending, Failed, or Cancelled — with a detailed reason message for each state.

                            ---

                            ## Billing

                            ### Metered Usage
                            DevPods are metered based on GPU type, GPU count, and runtime duration.

                            !!! info
                                Billing information is displayed in real-time during creation, based on your selected GPU type and count.
                            
