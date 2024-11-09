## Directory

- [简体中文](https://github.com/ray-d-song/web-archive/blob/main/docs/README_zh.md)
- [English](https://github.com/ray-d-song/web-archive/blob/main/README.md)

## Web Archive

![showcase](https://raw.githubusercontent.com/ray-d-song/web-archive/main/docs/imgs/showcase.gif)

Web Archive is a free web archiving and sharing service based on Cloudflare, including the following parts:  

- Browser plugin: Save the webpage as a single html file and upload it to the server.
- Server: Receive the html file uploaded by the browser plugin and store it in the database and storage bucket.
- Web client: Query the html file and display it.

The server is based on the full set of services of Cloudflare Worker, including D1 database and R2 storage bucket.

## Why
Most web archiving tools, like archivebox, are based on server-side calls to headless browsers to capture pages.  
This approach has the following drawbacks:  
- It is difficult to archive websites that require login, such as zhihu and Medium, as they need to configure tokens or cookies.
- Headless browsers require higher server requirements, and most users are nas users.
Web Archive is a completely free and zero-threshold solution, and Cloudflare can easily migrate the data back to the local host after self-hosting.

## Feat & Roadmap
- [x] Folder classification
- [x] Page preview image
- [x] Title keyword search
- [x] Showcase, share the pages you captured
- [x] Mobile support
- [ ] Tag classification system
- [ ] Save the page as markdown

## Deploy Guide
Github Actions (Recommended)  

[![Deploy to Cloudflare Workers](https://deploy.workers.cloudflare.com/button)](https://deploy.workers.cloudflare.com/?url=https://github.com/ray-d-song/web-archive)  

Click the button above, follow the instructions of Cloudflare to complete the deployment.  
 
> [!IMPORTANT]  
> R2 storage bucket is a feature that needs to be manually enabled in the Cloudflare panel, please enable it before deployment or re-run Github Actions after failure.
> You only need to enable the R2 feature, no need to create a storage bucket, the storage bucket will be created automatically during deployment.  

> [!NOTE]  
> When creating a token, select the `Edit Cloudflare Workers` template directly, and then manually add the `D1 Edit` permission.

![permissions](https://raw.githubusercontent.com/ray-d-song/web-archive/main/docs/imgs/perm.png)  

Once deployed, please login as soon as possible, the first user to login will be set as the administrator.

---

<details>
<summary>Command Deploy</summary>

Requires the local installation of the node environment.  
Updating during command deployment is more troublesome, it is recommended to use Github actions for deployment.  
### 0. Download the code
Download the latest service.zip from the release page, unzip it, and execute the following commands in the root directory.

### 1. Login
```bash
npx wrangler login
```

### 2. Create r2 bucket
```bash
npx wrangler r2 bucket create web-archive
```
Output:
```bash
 ⛅️ wrangler 3.78.10 (update available 3.80.4)
--------------------------------------------------------

Creating bucket web-archive with default storage class set to Standard.
Created bucket web-archive with default storage class set to Standard.
```

### 3. Create d1 database
```bash
npx wrangler d1 create web-archive
```

Output:

```bash
 ⛅️ wrangler 3.78.10 (update available 3.80.4)
--------------------------------------------------------

✅ Successfully created DB 'web-archive' in region UNKNOWN
Created your new D1 database.

[[d1_databases]]
binding = "DB" # i.e. available in your Worker on env.DB
database_name = "web-archive"
database_id = "xxxx-xxxx-xxxx-xxxx-xxxx"
```

Copy the last line of the output, and replace the `database_id` value in the `wrangler.toml` file.  

Then execute the initialization sql:
```bash
npx wrangler d1 migrations apply web-archive --remote
```

Output:
```bash
🌀 Executing on remote database web-archive (7fd5a5ce-79e7-4519-a5fb-2f9a3af71064):
🌀 To execute on your local development database, remove the --remote flag from your wrangler command.
Note: if the execution fails to complete, your DB will return to its original state and you can safely retry.
├ 🌀 Uploading 7fd5a5ce-79e7-4519-a5fb-2f9a3af71064.0a40ff4fc67b5bdf.sql
│ 🌀 Uploading complete.
│
🌀 Starting import...
🌀 Processed 9 queries.
🚣 Executed 9 queries in 0.00 seconds (13 rows read, 13 rows written)
   Database is currently at bookmark 00000001-00000005-00004e2b-c977a6f2726e175274a1c75055c23607.
┌────────────────────────┬───────────┬──────────────┬────────────────────┐
│ Total queries executed │ Rows read │ Rows written │ Database size (MB) │
├────────────────────────┼───────────┼──────────────┼────────────────────┤
│ 9                      │ 13        │ 13           │ 0.04               │
└────────────────────────┴───────────┴──────────────┴────────────────────┘
```

### 4. Deploy
```bash
npx wrangler pages deploy
```

Output:
```bash
The project you specified does not exist: "web-archive". Would you like to create it?
❯ Create a new project
✔ Enter the production branch name: … dev
✨ Successfully created the 'web-archive' project.
▲ [WARNING] Warning: Your working directory is a git repo and has uncommitted changes

  To silence this warning, pass in --commit-dirty=true

🌎  Uploading... (3/3)

✨ Success! Uploaded 3 files (3.29 sec)

✨ Compiled Worker successfully
✨ Uploading Worker bundle
✨ Uploading _routes.json
🌎 Deploying...
✨ Deployment complete! Take a peek over at https://web-archive-xxxx.pages.dev
```
</details>

## Usage Guide

Download the latest extension.zip from the release page, unzip it, and install it to the browser.  
After the first installation, you need to enter the API address and key. The API address is the service address, and the key is the password of the first user (administrator).  

Download the latest extension.zip from the release page, unzip it, and install it to the browser.  
After the first installation, you need to enter the API address and key. The API address is the service address, and the key is the password of the first user (administrator).  

1. Install the Browser Extension
   - Go to the releases page
   - Download the latest extension.zip
   - Unzip the file
   - In Chrome/Edge, go to chrome://extensions/
   - Enable "Developer mode"
   - Click "Load unpacked" and select the unzipped extension folder
2. Configure the Extension
   - After installation, you'll need to configure:
     - API Address: Use the URL from your deployment (like https://web-archive-xxxx.pages.dev)
     - Key: Set this as your desired admin password (this will be the password for the first user)
3. Access the Web Interface
   - Go to your deployed URL (e.g., https://web-archive-xxxx.pages.dev)
   - Log in using the key you set as the password
   - The showcase can be accessed at /#/showcase/folder
4. Using the Archive
   - Once set up, you can use the browser extension to archive pages
   - In the web interface, you can:
   - Organize archived pages into folders
     - Mark pages for showcase display
     - Access your archived content
     - Share archived pages


In the folder page, you can set whether a page is displayed in the showcase.  
Showcase address: /#/showcase/folder
