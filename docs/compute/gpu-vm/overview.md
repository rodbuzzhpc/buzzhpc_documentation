---
title: GPU Virtual Machines
description: Dedicated GPU-accelerated virtual machines with complete OS isolation, flexible networking, and persistent storage for AI and HPC workloads.
tags:
  - GPU VM
  -   - Virtual Machines
      -   - NVIDIA GPUs
          -   - H200
              -   - A40
                  -   - Deep Learning
                      -   - AI Training
                          -   - Dedicated GPU
                              - ---

                              Fully dedicated GPU-accelerated virtual machines providing complete OS-level isolation with persistent storage, flexible networking, and direct GPU passthrough for demanding AI and HPC workloads.

                              ---

                              ## Developer Experience

                              ### Dedicated Virtual Machines
                              GPU VMs provide full virtual machine isolation with dedicated GPU resources. Unlike containerized environments, GPU VMs offer complete OS control, persistent storage, and the ability to install any software stack.

                              ---

                              ### Multiple GPU Configurations
                              Choose from a wide range of GPU configurations:
                              - **NO-GPU-VM** - CPU-only VM for non-GPU workloads
                              - - **1xH200-GPU-VM** - Single NVIDIA H200 GPU
                                - - **2xH200-GPU-VM** - Dual NVIDIA H200 GPUs for distributed training
                                  - - **4xH200-GPU-VM** - Quad NVIDIA H200 GPUs for large-scale training
                                    - - **8xH200-GPU-VM** - 8x NVIDIA H200 GPUs for the largest AI models
                                      - - **a40-1gpu-vm** - Single NVIDIA A40 GPU for rendering and inference
                                       
                                        - ---

                                        ### SSH and Password Access
                                        Connect via SSH using auto-generated or custom SSH keys, or use the guest password for console access. Both private and public IP addresses are provided for flexible network access.

                                        ---

                                        ## Lifecycle Management

                                        ### Start, Stop, and Reboot
                                        GPU VMs support full lifecycle management directly from the portal. Start, stop, and reboot without needing to delete and recreate - ideal for cost management and maintenance windows.

                                        ---

                                        ### Re-publish
                                        Re-apply VM configuration or recover from a failed provisioning state without data loss.

                                        ---

                                        ### Status Monitoring
                                        Real-time status tracking: Success, Pending, Failed, or Cancelled - with detailed reason messages for each state.

                                        ---

                                        ## Networking

                                        ### Public and Private IP
                                        Each GPU VM is assigned a private IP. Users can optionally assign a public IP for direct external access.

                                        ---

                                        ### Firewall Rules
                                        Configure per-VM inbound firewall rules (Security Groups) with: Source CIDR, Rule Type (allow/deny), Application name, Destination Port, and Protocol (tcp/udp). Multiple rules can be added per VM.

                                        ---

                                        ### Port Forwarding
                                        Configure port forwarding to expose services running inside the VM on specific external ports.

                                        ---

                                        ## Storage

                                        ### Configurable Boot Disk
                                        Set the boot disk size (GB) at creation time.

                                        ---

                                        ### Shared Storage Mount
                                        Attach shared storage volumes at creation time for persistent, shared data access across multiple VMs.

                                        ---

                                        ## Security

                                        ### SSH Key Authentication
                                        Inject SSH public keys at VM creation for secure, passwordless authentication.

                                        ---

                                        ### Guest Password
                                        Configure a guest password at VM creation time for console or fallback authentication.

                                        ---

                                        ## Billing

                                        ### Metered Usage
                                        GPU VMs are metered by the hour based on GPU type and count. Billing begins at successful provisioning and stops when the VM is deleted.

                                        !!! info
                                            Always stop or delete GPU VMs when not in use to minimize costs. VMs with more GPUs (e.g., 8xH200) incur significantly higher hourly costs.
                                        
