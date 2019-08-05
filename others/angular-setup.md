# Angular Setup on mac

## Install Brew

<https://brew.sh/>

```cmd
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

## Install yarnpkg

<https://yarnpkg.com/en/docs/install#mac-stable>

```cmd
brew install yarn
```

OR

```cmd
sudo port install yarn
```

OR

```cmd
curl -o- -L https://yarnpkg.com/install.sh | bash
```

## Testing

Check the the installation by below command

```cmd
ng --version
```

If above command shows some entries in error. Install cli using below command

```cmd
npm install -g @angular/cli
```

## Resolve Errors

### Could not find module “@angular-devkit/build-angular”

```cmd
yarn add @angular-devkit/build-angular --dev
```

