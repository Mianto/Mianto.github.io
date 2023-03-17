---
title: "Restaurant Manager"
date: 2023-03-17T13:04:27+05:30
draft: true
ShowReadingTime: false
ShowPostNavLinks: false
ShowToc: false
cover:
  image: images/projects/restaurant-manager/app-ss-3.png
---

Restaurant Manager is a full-stack application using Spring-Boot and React, with features for both customers (scanning QR code, selecting menu items, submitting orders) and admins (viewing/creating orders). Implemented Spring Security 5 for authentication, used MySQL with Hibernate, and added private/non-private routes with react-router. The application offers both customer and admin journeys, which are detailed below.

### Customer Journey

- Customer will be able to scan QR code and go to webpage
- Customer will be able to choose items from Menu.
- Add menuItem to the cart.
- Submit cart to create an order.
- In cart customer can add a menuItem, remove a menuItem, change quantity of menuItem.
- Customer will require to login/sign-up before creating an order.

### Admin Journey

- Admin will be able to see list of customer order.
- Admin can create order with admin credentials.
- Admin can create order with customer credentials.
- Order will have following state: Draft -> Submit-> Waiting -> Served -> Completed.

# Technology Used

- Used SpringBoot application to create backend services.
- Used Spring Security 5, to filter resources and added JWT token to user remain authenticated for future requests.
- Used MySQL database with hibernate.
- Used React to create front-end for the app, using react hooks like useState, useEffect.
- Added private and non-private routes using react-router-dom.
- Used local-storage as cache for the storing data.

## Contribution and Feedback

The Project is open for feedback and contribution at [Github](https://github.com/Mianto/restaurant-manager)
