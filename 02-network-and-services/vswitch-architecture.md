# Phase 2 — vSwitch Architecture (Isolated Lab Network)

## Summary

This document outlines how the lab network was redesigned to use a dedicated **isolated vSwitch** in ESXi with **no physical uplink**. The goal is to keep all lab traffic contained inside the virtual environment and prevent any interference with the home network (especially around DHCP).

By separating the lab onto its own virtual switch and port group, the servers and client machines communicate only with each other on an internal subnet (192.168.11.0/24). This creates a controlled environment for testing Active Directory, DNS, DHCP, file sharing, and domain-joined clients without impacting anything outside the lab.

