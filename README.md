# koa-local-git-lfs
A local folder git-lfs server, with Koa server

[![GitHub release (latest by date)](https://img.shields.io/github/v/release/oOtroyOo/koa-local-git-lfs)](https://github.com/oOtroyOo/koa-local-git-lfs/releases/latest) [![Build Status](https://github.com/oOtroyOo/koa-local-git-lfs/actions/workflows/main.yml/badge.svg)](https://github.com/oOtroyOo/koa-local-git-lfs/actions/workflows/main.yml)

# Usage
## Download
  - Download Executeable from [Releases](https://github.com/oOtroyOo/koa-local-git-lfs/releases/latest) , Run exe
  - Clone Project, Run node

## Run args
  - `-p/--port NUMBER`
  
    Server Running Port


  - `-d/--dir STRING`
    
    Local folder Path


  - `(optional) -r/--route`

    Url Relative path, default (host:)`/lfs`

## Example

  - `koa-local-git-lfs.exe -d "./lfs_objects" -p 8888`
  - `node ./index.js -d "./lfs_objects" -p 8888`

# Koa Developer

  - `index.js`

``` js
import koa_local_git_lfs from 'koa-local-git-lfs'

// Required: Use a body parser middleware like 'koa-bodyparser'
import bodyParser from 'koa-bodyparser'

// Initialize Koa 'app' and 'router'

// Handle large file uploads: Skip body parsing for LFS object upload requests
// Because LFS might upload large files with ContentType:text which would cause bodyParser to fail
app.use(async (ctx, next) => {
    if (ctx.path.match('/objects/[a-fA-F0-9]{32,128}$')) ctx.disableBodyParser = true;
    await next();
});

// Configure bodyParser, Add json extendTypes to support application/vnd.git-lfs+json
app.use(bodyParser({
    //...other options
    extendTypes: { json: "+json" },
}));

// Initialize LFS service with object storage directory
const lfs = new koa_local_git_lfs(path.resolve(process.cwd(), 'lfs_objects'))

// Mount LFS routes
router.use("/lfs", lfs.routes())

// Start Koa server
```


# Client Git Respository Example

## Project Setup



  - create `.gitattributes` file
    - see: https://docs.github.com/repositories/working-with-files/managing-large-files/configuring-git-large-file-storage

    Example:
  ```
*.psd filter=lfs diff=lfs merge=lfs -text
*.png filter=lfs diff=lfs merge=lfs -text
*.exr filter=lfs diff=lfs merge=lfs -text
*.zip filter=lfs diff=lfs merge=lfs -text
*.tiff filter=lfs diff=lfs merge=lfs -text
*.tif filter=lfs diff=lfs merge=lfs -text
*.raw filter=lfs diff=lfs merge=lfs -text
*.fbx filter=lfs diff=lfs merge=lfs -text
*.jpg filter=lfs diff=lfs merge=lfs -text
*.wav filter=lfs diff=lfs merge=lfs -text
*.obj filter=lfs diff=lfs merge=lfs -text
*.aiff filter=lfs diff=lfs merge=lfs -text
*.tga filter=lfs diff=lfs merge=lfs -text
*.mp4 filter=lfs diff=lfs merge=lfs -text
*.webm filter=lfs diff=lfs merge=lfs -text
  ```

  - create `.lfsconfig` file
  
    Example:
  ```
  [lfs]
    url = http://127.0.0.1:8888/lfs/lfstest.git/
  ```


  - *(optional)* Moving file to Git LFS

    `git lfs migrate`
    - see: 
     https://docs.github.com/repositories/working-with-files/managing-large-files/moving-a-file-in-your-repository-to-git-large-file-storage

  - *(optional)* Force push to lfs

    `git lfs push --all origin `

# References
- https://github.com/git-lfs/git-lfs/blob/main/docs/api/server-discovery.md
- https://github.com/git-lfs/git-lfs/blob/main/docs/api/batch.md
- https://github.com/git-lfs/git-lfs/blob/main/docs/api/basic-transfers.md
- https://docs.github.com/repositories/working-with-files/managing-large-files/about-git-large-file-storage
- [BFG Repo-Cleaner](https://rtyley.github.io/bfg-repo-cleaner/)
