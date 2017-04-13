# mono-repo-history-rewrite

Procedure:  

## Create a branch in the android repo with files moved to where they will be in the mono repo with their history preserved:
```
cd mono-repo-test-android
git checkout -b rewrite
git ls-files -s | sed "s-	\"*-&android/-"
git filter-branch --index-filter \
  'git ls-files -s | sed "s-	\"*-&android/-" |
   GIT_INDEX_FILE=$GIT_INDEX_FILE.new \
   git update-index --index-info &&
   mv "$GIT_INDEX_FILE.new" "$GIT_INDEX_FILE"' rewrite
git push origin rewrite:rewrite
cd ..
```

## Do the same for iOS:
```
cd mono-repo-test-ios
git checkout -b rewrite
git ls-files -s | sed "s-	\"*-&ios/-"
git filter-branch --index-filter \
  'git ls-files -s | sed "s-	\"*-&ios/-" |
   GIT_INDEX_FILE=$GIT_INDEX_FILE.new \
   git update-index --index-info &&
   mv "$GIT_INDEX_FILE.new" "$GIT_INDEX_FILE"' rewrite
git push origin rewrite:rewrite
cd ..
```

## Do the same for React-Native:
```
cd mono-repo-test-react-native
git checkout -b rewrite
git ls-files -s | sed "s-	\"*-&react/-"
git filter-branch --index-filter \
  'git ls-files -s | sed "s-	\"*-&react/-" |
   GIT_INDEX_FILE=$GIT_INDEX_FILE.new \
   git update-index --index-info &&
   mv "$GIT_INDEX_FILE.new" "$GIT_INDEX_FILE"' rewrite
git push origin rewrite:rewrite
cd ..   
```   
   
## Make the mono repo:
```
mkdir mono-repo-history-rewrite
cd mono-repo-history-rewrite
git remote add origin git@github.com:lnanek/mono-repo-history-rewrite.git

git remote add android-subtree git@github.com:lnanek/mono-repo-test-android.git
git remote add ios-subtree git@github.com:lnanek/mono-repo-test-ios.git
git remote add react-native-subtree git@github.com:lnanek/mono-repo-test-react-native.git

git fetch android-subtree
git fetch ios-subtree
git fetch react-native-subtree

git merge --allow-unrelated-histories android-subtree/rewrite
git merge --allow-unrelated-histories ios-subtree/rewrite
git merge --allow-unrelated-histories react-native-subtree/rewrite
```

## Why we can't use git subtree command:
```
git subtree add android-subtree master
> You must provide the --prefix option.
```

## Just like "git mv ClassPass android" the history is lost on github if we do:
```
git subtree add --prefix=android/ android-subtree master
```
## Since github does not retrieve history using "git log --follow" for performance reasons.


## We're done, history is preserved:
```
git push -u origin master
```


