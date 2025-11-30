# kaayaka-ai-infra - deploy runbook

1. Edit manifests: replace placeholders (repo URL in ArgoCD, cert-manager issuer if needed).
2. Push infra repo to GitHub (e.g., https://github.com/YOUR_ORG/kaayaka-ai-infra).
3. Create namespace & secret via kubectl (recommended):
   kubectl -n ai create secret generic gemini-secret --from-literal=GEMINI_API_KEY="${GEMINI_API_KEY}"
4. Add DNS A record for ai.kaayaka.in pointing to your ingress controller's IP.
5. Ensure cert-manager has a ClusterIssuer named `letsencrypt-prod`.
6. Create ArgoCD Application (apply argo-application.yaml or create via ArgoCD UI).
7. Ensure backend/frontend repos have GitHub Actions secrets set: DOCKERHUB_USERNAME, DOCKERHUB_PASSWORD, (optional) ARGOCD_SERVER, ARGOCD_TOKEN.
8. Push code to backend & frontend `main` branches — CI will build & push images.
9. ArgoCD will sync infra repo and deploy. Check:
   kubectl -n ai get pods,svc,sts,ingress
10. To debug:
    kubectl -n ai logs deploy/gemini-backend
    kubectl -n ai logs deploy/gemini-frontend
    kubectl -n ai logs sts/redis
