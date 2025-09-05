
🔹 docker (container basics)

Q1. what’s the difference between a docker image and a container?
- 	answer: image = blueprint (read-only layers). container = running instance of an image.
- 	why: shows you know lifecycle.
- 	how: build once (image), run many (containers).

⸻

Q2. what are docker layers and why are they important?
- 	answer: each dockerfile instruction creates a layer. cached layers speed up builds.
- 	why: performance & reproducibility.
- 	how: put rarely changing steps first (os, deps) and changing steps last (code).

⸻

Q3. how do volumes differ from bind mounts?
- 	answer: volumes = managed by docker, persistent, portable. bind mounts = direct host path.
- 	why: data persistence matters in prod.
- 	how: volumes for dbs, caches; bind mounts for dev.

⸻

Q4. what’s the role of docker networks?
- 	answer: enable container-to-container communication.
- 	why: needed for microservices.
- 	how: use bridge networks for local dev, overlay networks in swarm/k8s.

⸻

Q5. common mistakes in dockerfiles for go apps?
- 	answer:
- 	copying entire repo instead of only what’s needed.
- 	not using multi-stage builds → bloated images.
- 	running as root.
- 	why: performance & security.
- 	how: build binary in golang image → copy into alpine/distroless.

⸻

🔹 kubernetes (k8s)

Q1. difference between pods, deployments, and services?
- 	answer:
- 	pod = smallest deployable unit (1+ containers).
- 	deployment = manages replica sets (scaling, rolling updates).
- 	service = stable network endpoint to access pods.
- 	why: shows grasp of abstractions.
- 	how: deployment ensures self-healing/scaling, service handles load-balancing.

⸻

Q2. what are configmaps vs secrets?
- 	answer: configmaps = non-sensitive config (urls, flags). secrets = sensitive data (tokens, passwords).
- 	why: config should be externalized.
- 	how: mount as env vars/files; use k8s secrets with encryption at rest.

⸻

Q3. how do you expose a service externally?
- 	answer: service type = loadbalancer (cloud provider lb), nodeport (direct node ip+port), ingress (http routing).
- 	why: networking is core.
- 	how: for apis, ingress with tls termination is best practice.

⸻

Q4. how does horizontal pod autoscaling (hpa) work?
- 	answer: scales pod replicas based on metrics (cpu/memory/custom).
- 	why: elasticity = cost-efficient.
- 	how: configure hpa with target cpu%, min/max replicas; needs metrics-server.

⸻

Q5. what’s the difference between readiness and liveness probes?
- 	answer:
- 	readiness = is pod ready to serve traffic?
- 	liveness = is pod still healthy, or should it be restarted?
- 	why: prevents routing to dead pods; ensures auto-healing.
- 	how: http/tcp/exec probes in pod spec.

⸻

Q6. how do rolling updates and rollbacks work in k8s?
- 	answer: deployment updates pods gradually; if failure, rollback to last working replica set.
- 	why: zero downtime deployments.
- 	how: kubectl rollout status deployment/..., kubectl rollout undo ....

⸻

Q7. how does k8s handle secrets securely?
- 	answer: stored base64-encoded by default, but should be encrypted at rest with kms.
- 	why: interviewer tests security awareness.
- 	how: enable encryption at rest; integrate with vault/aws secrets manager.

⸻

Q8. difference between statefulset vs deployment?
- 	answer: deployment = stateless apps (api servers). statefulset = apps needing stable identity/storage (databases).
- 	why: tests data persistence understanding.
- 	how: statefulset guarantees stable pod names & volume claims.

⸻

Q9. how do you debug a pod that won’t start?
- 	answer: check kubectl describe pod, look at events (imagePullBackOff, crashloopbackoff). use kubectl logs for container logs.
- 	why: debugging flow matters.
- 	how: common issues = bad image, missing secret/config, resource limits.

⸻

🔹 performance & pitfalls

docker pitfalls:
- 	bloated images (fix: multi-stage builds).
- 	no healthcheck in dockerfile.
- 	root user (fix: non-root).

k8s pitfalls:
- 	ignoring resource requests/limits → noisy neighbors.
- 	no probes → downtime on broken pods.
- 	over-replicating consumers vs kafka partitions (idle pods).
- 	storing secrets in configmaps → insecure.
- 	no network policies → wide open traffic.

⸻

✅ must-know for interviews
- 	docker: image vs container, layers, volumes, networks, best practices for go dockerfiles.
- 	k8s: pod vs deployment vs service, configmaps vs secrets, probes, hpa, ingress, rolling updates, statefulset vs deployment.
- 	security: run as non-root, encrypt secrets, use resource limits, apply network policies.
- 	ops: how to debug pods, how scaling works, how service discovery works
