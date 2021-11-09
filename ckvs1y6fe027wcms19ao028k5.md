## GitHub Copilot: Intro, Benefits and Examples

Recently I've been invited to the GitHub Copilot technical preview, and I can tell you it deserves all the attention it's getting!

So first things first..

### What is GitHub Copilot?

GitHub Copilot is an AI pair programmer that helps you write code faster and with less work. It draws context from comments and code, and suggests individual lines and whole functions instantly. GitHub Copilot is powered by OpenAI Codex, a new AI system created by OpenAI.

The GitHub Copilot technical preview is available as an extension for Visual Studio Code, Neovim, and the JetBrains suite of IDEs.

### How does GitHub Copilot work?

![copilot.jpeg](https://cdn.hashnode.com/res/hashnode/image/upload/v1635954882803/VGqp16ZMK.jpeg)

The GitHub Copilot editor extension sends your comments and code to the GitHub Copilot service, which then uses OpenAI Codex to synthesize and suggest code. it actually works by reading through all the open-source code on the GitHub repos worldwide and then collect the data and tries to find the best possible code related to it! It is said to work great with repetitive code patterns so users can let it generate the rest of the code. The AI assistant can also help you learn a new programming language.

It can suggest complete lines of code or entire functions by analyzing how you code. GitHub Copilot can assemble code from user comments and predicts your code by just reading the function name you have declared. It allows you to cycle through alternative suggestions and manually edit the suggested code. It autofill repetitive code, or create unit tests for your methods.


### Does GitHub Copilot write perfect code?

Nope! GitHub Copilot tries to understand your intent and to generate the best code it can, but the code it suggests may not always work or even make sense.

"*GitHub Copilot draws context from the code you’re working on, suggesting whole lines or entire functions*", GitHub CEO Nat Friedman explained in a [blog post](https://github.blog/2021-06-29-introducing-github-copilot-ai-pair-programmer/)  introducing the technology. The algorithm consistently improves by recording whether each suggestion is accepted or not. 

**Note**: that code suggested by GitHub Copilot should be carefully tested, reviewed, and vetted, like any other code. As a developer, you are always in charge.


### Let's check out few examples!

Remember, in order to make the most out of it, it is suggested to divide the code into smaller functions, provide meaningful function names and parameters.

According to GitHub, it understands dozens of languages but it does especially well for Python, JavaScript, TypeScript, Ruby, Java, and Go.

Now let's have some fun here with a blank Javascript file and start with some straight-forward "pure" functions like `logMessage`, `addTwoNumbers` and `calcDiffBetweenDates` and see what we get.

![log.gif](https://cdn.hashnode.com/res/hashnode/image/upload/v1636375831921/F1m-DZIkG.gif)

Even it could autofill a function based on a comment.

![comment.gif](https://cdn.hashnode.com/res/hashnode/image/upload/v1636378154097/6YfycP71Q.gif)

Now what about functions expected to be using some APIs like Twitter?

![twitter.gif](https://cdn.hashnode.com/res/hashnode/image/upload/v1636375646200/rBBu6G9Se.gif)

Next with AWS SDK functions like: `uploadToS3`, `getSignedUrlS3`, `insertIntoDynamodb`.

![aws.gif](https://cdn.hashnode.com/res/hashnode/image/upload/v1636376121024/W7xvMlZ1f.gif)

Ah one of the most developer's time waster: Regular Expressions 😆 Let's test the email and URL validation regex.

![regex.gif](https://cdn.hashnode.com/res/hashnode/image/upload/v1636376431854/AItFvoGa_.gif)

And how far can it fetches a correct answer for Leetcode problems? starting with an easy one like `binarySearch` to another hard one like `longestValidParentheses`

![leetcode.gif](https://cdn.hashnode.com/res/hashnode/image/upload/v1635952371820/I4YzRTcyk.gif)

What about some CSS class selectors in a blank CSS file?

![css.gif](https://cdn.hashnode.com/res/hashnode/image/upload/v1636374384290/_1RGW2lHx.gif)

Does it help me get some emojis?

![emj.gif](https://cdn.hashnode.com/res/hashnode/image/upload/v1636377134154/yj0vZilh0.gif)

Let's try random stuff: `whatIsTheMeaningOfLife` returns back 42 which is a number significant to fans of science fiction novelist Douglas Adams' "The Hitchhiker's Guide to the Galaxy" because that number is the answer given by a supercomputer to "the Ultimate Question of Life, the Universe, and Everything." 😀

![life.gif](https://cdn.hashnode.com/res/hashnode/image/upload/v1636377018937/Pnkz3tylh.gif)

Okay I bet you can spend the whole day playing around with this among different use-cases with different programming languages. It could be also way more useful within the context of your application.

### Alternatives to GitHub Copilot

Also worth to mention; there are few other alternatives that provides auto completion powered by AI connected to the developers' editors like  [Kite](https://www.kite.com/) and [TabNine](https://www.tabnine.com/).

### Wanna try GitHub Copilot out?

The best way to contribute is to sign up for the technical preview. Access is limited to a small group of testers during the technical preview of GitHub Copilot, so you can Join the GitHub Copilot waitlist [here](https://github.com/features/copilot/signup) .


### That's it!

More about it, check [GitHub Copilot Docs](https://github.com/github/copilot-docs)  out!

Also check out Twitter's hashtag [#GitHubCopilot](https://twitter.com/hashtag/GitHubCopilot) for more from the community.

And if you don't have access to technical preview yet, please let me know if you are curious about some snippets that I could add here later. 😉

Enjoy coding faster with Github Copilot! 🤖 