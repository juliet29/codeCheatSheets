# Github Cheat Sheet 

* If initialize online, just do automatic and don't change anything! 

* Initialize a repo on the command line 
    ```bash 
    touch README.md
    git init
    git add README.md
    git commit -m "first commit"

    git remote add origin https://github.com/juliet29/bthermal_model
    git push -u origin master
    ```




* If mess up making a repo
    ``` bash
    git remote rm origin

    undo git add
    git reset . 

    undo a git init
    rm -rf .git

    set a new origin 
    git remote set-url origin git://new.url.here
    ```