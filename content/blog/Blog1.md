Title: How to Create a Static Page on Github 
Date: 2020-08-25 20:55
headline:Yiping's Blog1


This blog mainly talk about how to start a personal website on gitHub pages use pelican. 





## Step 1: Set up github.io page
Create two new repositories: ys279.github.io-src and ys279.github.io.
The ys279.github.io-src repository will hold the sources of blog and the ys279.github.io repository will contain the output HTML files Pelican generates.

## Step 2: Install Pelican 

Install Pelican and Markdown by running the following command :  

```
pip install pelican 
```

```
pip install pelican markdown
```

Then Clone the source repository and change directory to the site:

```
git clone https://github.com/ys279/ys279.github.io-src/tree/master ss
```
```
cd ss
```

## Step 3: Create a Blog
Clone the output repository as a git submodule :

```
git submodule add https://github.com/ys279/ys279.github.io output
```

Then Create a skeleton project via the pelican-quickstart command, and give the answer 
URL prefix: http://ys279.github.io  

Write a .md file(Blog1.md) in content folder as the first blog.
Also can change the site theme with pelican theme.

## Step 4 :Generate my site, commit and push

From my site directory, run the pelican command to generate site:
```
pelican content
```

test result using:
```
make serve
```
and point browser to localhost:8000

If  everything is OK, generate the website:
```
make publish
```
Then add  files to git tracking, commit them, and push to the repositories. 
```
cd output
git add .
git commit -m "Final post."
git pull
echo '*.pyc' >> .gitignore 
git add .
git commit -m "First commit."
git push -u origin master
```


Now, you can visit the https://ys279.github.io and see the new site I’ve created.