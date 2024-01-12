---
title: GIT
icon: material/git
---

# **Wiki - GIT**

## Créer un nouveau référentiel en ligne de commande
```
echo "# test" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin https://github.com/allfab/XXXXX.git
git push -u origin main
```

## Pousser un référentiel existant en ligne de commande
```
git remote add origin https://github.com/allfab/XXXXX.git
git branch -M main
git push -u origin main
```

## GIT Large File System initialisation
```
git lfs install
git lfs track "*.bin.zip"
git add .gitattributes

git add docker-compose/stacks/igeo-stack/gdal/resources/hexagon/ECWJP2SDKSetup_5.5.0.2268.bin.zip
git commit -m "ADD ECW/JP2 FILE support"
git push origin main
```