## How to Delete "node_modules" In a Style

For Javascript projects, the `node_modules` folder is used to save all downloaded packages from `npm` locally in your computer. JS developers often complain that their hard drive is mysteriously always full.

![StorageFull.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1635113199346/owhOSuMep.png)

Every time you start a new (side) project and enter `npm install` it downloads a bunch of JS files into the `node_modules` folder that you totally forget about after you abandon this project.


![npminstall.jpg](https://cdn.hashnode.com/res/hashnode/image/upload/v1635149056855/So6z4hLAh.jpeg)

You could go to every `node_modules` folder and click "Right Click" then "Delete" or you can use the following command for Mac / Linux OS:

```
rm -rf node_modules
```
The command above will first delete the content of `node_modules` recursively until all of it is deleted, then it will remove the `node_modules` folder too.

If you are a senior developer, you might create a bash script to find all the `node_modules` folder and delete them as follow:

```
find . -name "node_modules" -type d -prune -exec rm -rf '{}' +
```

But here is how you can delete your `node_modules` folder in a style which I find it the smartest way to get the job done is to run:
```
npx npkill
```
It will traverse your file system and find all the `node_modules` folders that can be removed, give you a breakdown of their sizes and allow you to delete them one by one by hitting the space bar.

![NpxNpkill.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1635112911992/H0qQDTgUb.png)

That's it!

For more details, check out  [npkill package](https://www.npmjs.com/package/npkill).

I'd love for you to leave me a feedback below in the comments!