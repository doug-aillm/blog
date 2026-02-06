# Blog Upgrade Implementation Status

## Phase 1: Giscus Comments ⏳ IN PROGRESS

### ✅ Completed:
1. Enabled GitHub Discussions on blog repo (via `gh repo edit`)
2. Created `Comments.astro` component with Giscus integration
3. Retrieved repo ID: R_kgDONIR60A

### 🔧 Next Steps:
1. **Manual step needed:** Install Giscus GitHub App
   - Go to: https://github.com/apps/giscus/installations/new
   - Select: doug-aillm/blog repository
   - Click Install (requires 2FA)
   
2. Create "Blog Comments" category in GitHub Discussions
   - Type: Announcements (only maintainers can create new discussions)
   - Get category ID for configuration

3. Update `[...slug].astro` to import and use Comments component

4. Test on a blog post

### Configuration Values:
- **Repo:** doug-aillm/blog
- **Repo ID:** R_kgDONIR60A
- **Category:** Blog Comments (to be created)
- **Category ID:** (pending)

---

## Phase 2: Umami Analytics 📊 QUEUED

Self-hosted analytics on Vercel free tier.

## Phase 3: Email Subscriptions 📧 QUEUED

Start with Buttondown (free tier), then evaluate Listmonk for self-hosting.

---

**Total Cost: $0/month** ✅
