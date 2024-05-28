# My HomeLab Documentation

## Summary

Business-class SDN network with Mesh support and dedicated NAS PC. ISP Modem/Router is in bridge mode.

## Table of Contents

- [Physical Network Map](./maps/physical.map.pdf)
- Logical Network Map
- [IP Address Assignment](./ip.addresses.md)
- Network Controller Configuration
- [NAS Server Configuration](./config/nas.md)
- Disaster Recovery
- Floor Plan

## Pain Points

- Building floors and walls are thick concrete. No hardwired connections are possible, except to the main router.
- Single router offers adequate coverage for one floor, but minimal to no coverage for the other 3 as well as outside.
- Very limited budget, and will have to utilize hardware that I have on-hand.
- Switch cannot be adopted by Omada Controller. Has to be configured as standalone. Known issue.

## Areas to improve

- Wired backhaul to each AP would improve performance.
- APs are previous-generation (WiFi 5/AC), and will need to be upgraded later.
- NAS system would benefit with an upgrade.
