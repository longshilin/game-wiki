# Git dependencies
When the Package Manager fetches a package from a Git repository, it adds the package locally to your project. This allows you to easily test unpublished changes, but you cannot use it to contribute to that Git repository. To set up an existing local Git repository as a **dependency**  in your project, use a [path to your local Git repository](https://docs.unity3d.com/Manual/upm-localpath.html) instead.

You cannot specify **Git dependencies**``  in a [package.json](https://docs.unity3d.com/Manual/upm-manifestPkg.html) file because the Package Manager does not support Git dependencies between packages. It only supports Git dependencies for projects, so you can only declare Git dependencies in the project’s [manifest.json](https://docs.unity3d.com/Manual/upm-manifestPrj.html) file.

This section includes the following topics:

-   [Requirements](https://docs.unity3d.com/Manual/upm-git.html#req)
    
-   Git URLs and extended syntax
    
    -   [Using the HTTP/HTTPS protocol](https://docs.unity3d.com/Manual/upm-git.html#Git-HTTPS)
        
    -   [Using the SSH protocol](https://docs.unity3d.com/Manual/upm-git.html#Git-SSH)
        
    -   [Using the FILE protocol](https://docs.unity3d.com/Manual/upm-git.html#Git-FILE)
        
    -   [Using the GIT protocol](https://docs.unity3d.com/Manual/upm-git.html#Git-GIT)
        
    -   [Targeting a specific revision](https://docs.unity3d.com/Manual/upm-git.html#revision)
        
    -   [Specifying a package in a subfolder](https://docs.unity3d.com/Manual/upm-git.html#subfolder)
        
-   [Locked Git dependencies](https://docs.unity3d.com/Manual/upm-git.html#git-locks)
    
-   [Git LFS Support](https://docs.unity3d.com/Manual/upm-git.html#git-lfs)
    

## Requirements

To use Git dependencies in a project, make sure the [Git client](https://git-scm.com/) is installed on your machine and that you have added the Git executable path to the PATH system environment variable.

If the repository tracks files with [Git LFS](https://git-lfs.github.com/), make sure the Git LFS client is also installed on your machine. If it is not installed, the Package Manager can’t retrieve the files stored on the LFS server and instead checks out the LFS pointer files without any error or warning messages.

You can use the Package Manager window to install a package directly from a Git repository. For more information, see [Installing from a Git URL](https://docs.unity3d.com/Manual/upm-ui-giturl.html).

## Git URLs and extended syntax

The Package Manager supports all [Git protocols](https://git-scm.com/book/en/v2/Git-on-the-Server-The-Protocols), with the exception of direct file paths. To specify a Git URL as a dependency, add the name of the package to add with a Git URL instead of the version number or local file path. For example, this demonstrates how to specify a remote Git using different protocols:

{  
 "dependencies": {  
 "com.mycompany.mypackage1": "https://github.example.com/myuser/myrepository1.git",  
 "com.mycompany.mypackage2": "ssh://git@github.example.com/myuser/myrepository2.git",  
 "com.mycompany.mypackage3": "file://localhost/github.example.com/myuser/myrepository3.git",  
 "com.mycompany.mypackage4": "git://github.example.com/myuser/myrepository4.git",  
 etc.  
 }  
}

The Package Manager recognizes that a dependency formatted as a URL is a Git URL by looking for the `.git` file extension at the end of the repository path. Some Git repository hosting services do not support URLs with this extension while others enforce it. For this reason, the Git dependency syntax allows you to omit the extension if you use the [GIT protocol](https://docs.unity3d.com/Manual/Git-GIT), or if you add a special `git+` prefix to the [HTTP/HTTPS](https://docs.unity3d.com/Manual/upm-git.html#Git-HTTPS), [SSH](https://docs.unity3d.com/Manual/upm-git.html#Git-SSH), or [FILE](https://docs.unity3d.com/Manual/upm-git.html#Git-FILE) URL.

**Note:** The `git+` prefix is a special marker in the `manifest.json` file that indicates that the dependency is Git-based. The Package Manages does not pass it to Git when cloning the repository.

For more information about the format of Git-supported URLs, see the documentation for the git [clone](https://git-scm.com/docs/git-clone#_git_urls_a_id_urls_a) command and [Git server protocols](https://git-scm.com/book/en/v2/Git-on-the-Server-The-Protocols).

In addition, Git dependencies use an extended syntax:

-   If the package you want is not at the root of the repository, you can specify a path to a subfolder in the repository where the package is located. This is only necessary if the package you want is not at the root of the repository. For example, the string `?path=/folder1/folder2` in:
    
    `"https://github.example.com/myuser/myrepository.git?path=/folder1/folder2"`.
    
    For more information, see [Specifying a package in a subfolder](https://docs.unity3d.com/Manual/upm-git.html#subfolder).
    
-   You can specify a Git revision, which can be a tag, branch name, or a specific commit hash to lock onto. This ensures that the Package Manager always loads that exact revision. If you don’t specify a revision, the Package Manager clones the repository at the default branch and latest commit. For example, the string `#v2.0.0` in:
    
    `"https://github.example.com/myuser/myrepository.git#v2.0.0"`
    
    For more information, see [Targeting a specific revision](https://docs.unity3d.com/Manual/upm-git.html#revision).
    

### Using the HTTP/HTTPS protocol

You can use the HTTPS protocol with the full URL:

{  
 "dependencies": {  
 "com.mycompany.mypackage": "https://github.example.com/myuser/myrepository.git"  
 }  
}

If your Git server does not support the `.git` extension, you can add the special `git+` prefix, with or without the extension:

{  
 "dependencies": {  
 "com.mycompany.mypackage1": "git+https://github.example.com/myuser/myrepository1.git",  
 "com.mycompany.mypackage2": "git+https://github.example.com/myuser/myrepository2"  
 }  
}

**Note**: Alternatively, you can use the GIT protocol instead of the `git+` prefix. For more information, see [Using the GIT protocol](https://docs.unity3d.com/Manual/upm-git.html#Git-GIT).

If the repository is publicly accessible, HTTPS is the recommended scheme for sharing Git URLs with users because you can copy and paste the URL directly from the Git repository hosting service web page.

![Copy the URL from the package repository](https://docs.unity3d.com/uploads/Main/upm-git-cloning.png)Copy the URL from the package repository

If the repository is not publicly accessible and you are using HTTPS, the repository server fails to authenticate you because you can’t interact with the server to provide your credentials. To work around this, choose one of the following solutions:

-   Authenticate beforehand using a [Git credentials helper](https://git-scm.com/docs/gitcredentials).
    
-   Include your username and password directly in the Git URL.
    
    **Warning**: This option is not recommended because it is not secure.
    
-   Use the [SSH protocol](https://docs.unity3d.com/Manual/upm-git.html#Git-SSH) instead. If you set up and configure an SSH key pair with the Git repository hosting service, the Package Manager can authenticate the request seamlessly on your behalf.
    

### Using the SSH protocol

You can use the SSH protocol with the full URL:

{  
 "dependencies": {  
 "com.mycompany.mypackage": "ssh://git@mycompany.github.com/gitproject/com.mycompany.mypackage.git"  
 }  
}

If your Git server does not support the `.git` extension, you can add the special `git+` prefix, with or without the extension:

{  
 "dependencies": {  
 "com.mycompany.mypackage1": "git+ssh://git@github.example.com/myuser/myrepository1.git",  
 "com.mycompany.mypackage2": "git+ssh://git@github.example.com/myuser/myrepository2"  
 }  
}

**Note**: Alternatively, you can use the GIT protocol instead of the `git+` prefix. For more information, see [Using the GIT protocol](https://docs.unity3d.com/Manual/upm-git.html#Git-GIT).

You can also use the SCP-like shorthand, which the Package Manager always recognizes as a Git dependency:

{  
 "dependencies": {  
 "com.mycompany.mypackage": "git@mycompany.github.com:gitproject/com.mycompany.mypackage.git"  
 }  
}

#### Using PuTTY on Windows

Git uses the keys at the default location when you use SSH to authenticate. However, if you are using [PuTTY](https://www.putty.org/) as the SSH client on Windows, you need to configure the `GIT_SSH` environment variable to make it point to `plink.exe`.

#### Authenticating with SSH

If you want to use the SSH protocol, you need to set up SSH keys outside of Unity. For more information on setting up authentication for a specific host, see the help pages for [Bitbucket](https://support.atlassian.com/bitbucket-cloud/docs/set-up-an-ssh-key/), [GitLab](https://docs.gitlab.com/ee/user/profile/personal_access_tokens.html), and [GitHub](https://github.com/settings/tokens).

**Note**: If you encrypted your SSH key with a passphrase, the Package Manager can’t retrieve the package, because it doesn’t provide a way to enter the passphrase in a terminal or command line. In this case, you can either use the [HTTPS protocol with a Git credential helper](https://docs.unity3d.com/Manual/upm-git.html#protocols) or use Git’s built-in SSH authentication [ssh-agent](https://www.ssh.com/ssh/agent). On Windows, if you are using PuTTY, you can use its [Pageant](https://www.ssh.com/ssh/putty/putty-manuals/0.68/Chapter9.html) authentication helper.

If you don’t have proper access, the Editor notifies you that authentication failed. For more information, see [Authentication issues with Git URLs](https://docs.unity3d.com/Manual/upm-errors.html#ssh-add).

### Using the FILE protocol

The Package Manager does not recognize Git URLs with the `file:` prefix as Git dependencies unless they are properly formatted. This means you must use either the `git+file:` protocol, or the `.git` suffix with the `file:` protocol:

{  
 "dependencies": {  
 "com.mycompany.mypackage1": "git+file://github.example.com/myuser/myrepository1",  
 "com.mycompany.mypackage2": "git+file:///github.example.com/myuser/myrepository2",  
 "com.mycompany.mypackage3": "file:///github.example.com/myuser/myrepository3.git"  
 }  
}

**Note**: Alternatively, you can use the GIT protocol instead of the `git+` prefix. For more information, see [Using the GIT protocol](https://docs.unity3d.com/Manual/upm-git.html#Git-GIT).

The Package Manager interprets any other syntax as a [local path](https://docs.unity3d.com/Manual/upm-localpath.html) instead.

### Using the GIT protocol

The Package Manager recognizes the `git:` protocol, with or without the `.git` path suffix:

{  
 "dependencies": {  
 "com.mycompany.mypackage1": "git://github.example.com/myuser/myrepository1.git",  
 "com.mycompany.mypackage2": "git://github.example.com/myuser/myrepository2"  
 }  
}

The GIT protocol doesn’t need or support the `git+` prefix.

### Targeting a specific revision

To declare a specific revision that you want the Package Manager to clone, add the revision prefixed by the number sign (`#`) at the end of the URL:

{  
 "dependencies": {  
 "com.mycompany.mypackage1": "https://github.example.com/myuser/myrepository1.git#revision",  
 "com.mycompany.mypackage2": "git+https://github.example.com/myuser/myrepository2#revision"  
 }  
}

The revision can be any tag, branch or commit hash. This table shows examples for specifying revisions:

**Syntax:**

**URL example**

Latest default branch

`"https://github.example.com/myuser/myrepository.git"`

Specific branch

`"https://github.example.com/myuser/myrepository.git#my-branch"`

Specific version

`"https://github.example.com/myuser/myrepository.git#v2.0.0"`

Commit hash

`"https://github.example.com/myuser/myrepository.git#9e72f9d5a6a3dadc38d813d8399e1b0e86781a49"`

### Specifying a package in a subfolder of the repository

If you specify a repository using the Git URL syntax, the Package Manager assumes that the package must be at the root of the repository. However, some packages are not located at the root level of their repository, and some repositories contain more than one package.

You can use the `path` query parameter in the Git URL to notify the Package Manager where to find the package. The path you specify must be relative to the root of the repository, and the subfolder you specify must contain the **package manifest**  (`package.json` file).

To specify a repository subfolder for a Git dependency, use the `path` query parameter:

{  
 "dependencies": {  
 "com.mycompany.mypackage": "https://github.example.com/myuser/myrepository.git?path=/subfolder"  
 }  
}

In this case, the Package Manager only registers the package located in the specified repository subfolder and disregards the rest of the repository.

Sometimes a repository contains several related packages. If you want to add more than one package from the same repository, you must add two separate entries to your **project manifest**``  :

{  
 "dependencies": {  
 "com.mycompany.mypackage1": "https://github.example.com/myuser/myrepository.git?path=/subfolder1",  
 "com.mycompany.mypackage3": "https://github.example.com/myuser/myrepository.git?path=/subfolder2/subfolder3"  
 }  
}

**Note**: If you specify the same repository multiple times, the Package Manager clones the same repository multiple times, which leads to reduced performance and additional network usage.

#### Revisions

The `path` query parameter always precedes the revision anchor. The reverse order fails. This is an example of the correct order to use:

{  
 "dependencies": {  
 "com.mycompany.mypackage": "https://github.example.com/myuser/myrepository.git?path=/example/folder#v1.2.3"  
 }  
}

## Locked Git dependencies

One of the core principles of the Package Manager is determinism. If you share your project with other users, the Package Manager should install the same set of package dependencies and versions, and that includes packages that it fetchs from Git. To achieve this, the Package Manager uses a [lock file](https://docs.unity3d.com/Manual/upm-conflicts-auto.html), which tracks which commit hashes of Git dependencies are used. For example, here is an entry in the lock file for a Git dependency:

"com.mycompany.mypackage": {  
 "version": "git@github.example.com/myuser/myrepository.git",  
 "depth": 0,  
 "source": "git",  
 "dependencies": {  
 "com.unity.package-validation-suite": "0.10.0-preview",  
 "com.unity.nuget.newtonsoft-json": "2.0.0-preview"  
 },  
 "hash": "6758e2800ac87cb37a9a8179610f92844a85cf7b"  
},  
 etc.

### Updating a locked version

When you add a Git dependency with a revision set to a branch or a tag, the Package Manager fetches the corresponding commit hash to store in the lock file. Over time, branches and tags can point to different commits on the Git repository. For example, a branch can have newer commits added to it.

To force the Package Manager to update the package to the new commit that a branch or tag points to, use one of these methods:

-   Manually remove the corresponding package section from the `packages-lock.json` file. The next time you trigger a package resolution process (for example, when Unity regains application focus), the Package Manager fetches the branch head or tag and then adds it to the lock file.
    
-   Create a script with the [Client.Add](https://docs.unity3d.com/ScriptReference/PackageManager.Client.Add.html) C# API method with the same Git dependency.
    

## Git LFS Support

The Package Manager supports Git dependencies with repositories using Git LFS. Since Git LFS has been designed to work with minimal configuration overhead, it supports both HTTPS and SSH [authentication](https://docs.unity3d.com/Manual/upm-git.html#authentication). Retrieval of files stored on the LFS server fails if users need to be authenticated and do not have valid credentials with permission to access the remote repository.

Package authors can help the Git LFS client locate the LFS Server by providing it with the URL in the `.lfsconfig` configuration file in the repository. There are two ways to do this:

# Option 1: global setting  
[lfs]  
 url = ssh://git@HOSTNAME/path/to/repo.git  
​  
# Option 2: per-remote setting  
[remote "origin"]  
 lfsurl = ssh://git@HOSTNAME/path/to/repo.git

If the repository contains a `.lfsconfig` file, make sure you include it in the `.npmignore` file to avoid including it in published releases of the package.
