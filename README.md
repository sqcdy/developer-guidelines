# Square Candy Design Developer Guidelines

## Getting Started

* install the theme or plugin on your WordPress site
* make sure npm is installed on your local server
* run `npm install && grunt init` in the theme directory
* run `grunt` to listen for changes to your scss files and to compile them immediately as you work

## Making Commits

All commits must follow the [Conventional Commits](https://www.conventionalcommits.org/) standard. The most basic way to use this is to just make sure your bugfix commit messages start with `fix: ` and new features start with `feat: `. Other prefixes such as `chore:`, `docs:` and `style:` can also be used.

Using Conventional Commits properly will result in a useful automatically generated change log that shows the new features and bugfixes in an organized way, but doesn't become poluted with commit messages from documentation updates, CI changes, cleanup tasks and release info that does not belong in a change log.

The Conventional Commits standard also helps us automate Semantic Versioning, bumping minor verion if there are `feat:` commits, and only bumping the patch version if there are only `fix:` and other non-feature commits.

Before you commit, make sure `grunt preflight` passes. This will run all of the automated linting that will also be run by the GitHub Workflow CI when you push your commit.

## Branching

For projects with only one primary developer working on it at a time, new development work can happen directly on the `develop` branch. This is a time-saving shortcut that we can only use while our team is still very small. For any project that has two or more people working on it, please follow the more formal [Branching Guidelines](/branching-guidelines).

##### Semantic Versioning

As we assign version numbers to our software, we follow [the Semantic Versioning pattern](http://semver.org/), wherein each version follows a MAJOR.MINOR.PATCH scheme:

* **MAJOR** versions are incremented when breaking changes are introduced, such as functionality being removed or otherwise major changes to the codebase.
* **MINOR** versions are incremented when new functionality is added in a backwards-compatible manner.
* **PATCH** versions are incremented for backwards-compatible bugfixes.

## Deployment Guidelines

* All release commits should happen on the `master` branch.
* Make sure `grunt preflight` passes.
* Run `npm run release -- --dry-run` to test for issues
* Run `npm run release --` to run the default release level (See Conventional Commits above)
* Run `npm run release -- --release-as minor` to force run a minor version release ( x.1.x > x.2.0 )
* Run `npm run release -- --release-as 1.2.3` to force a specific version number
* see the [standard-version README](https://github.com/conventional-changelog/standard-version) for more info or run `npm run release -- --help`

The `npm run release --` command will do everything you need to do to prepare the release automatically:
* bumps the version number in all of the different places it exists
* writes a change log entry for the release
* creates a tag for the release

> Note: Never create GitHub "Releases" in the online GUI. Tags will act as releases automatically, but if you use the manual releases feature GitHub Updater can interpret older items as the "latest version" even if a newer tag is introduced.

## Linting Guidelines

Run `grunt preflight` to run all file linting before you commit. All commits should pass preflight before being committed. Automated linting ensures that all code is clean and follows the [WordPress Coding Standards](https://make.wordpress.org/core/handbook/best-practices/coding-standards/) and also applies some custom tweaks specific to Square Candy. 

### PHP

* Test PHP, all files: `grunt phpcs`
* Test one PHP file: `phpcs path/to/example-file.php`
* Autofix all PHP files `phpcbf *.php */**.php` - note: there may still be errors that must be fixed by hand!
* Autofix one PHP file: `phpcbf path/to/example-file.php`
* In special circumstances, you can override rules using `phpcs:ignore rule.name`. See the [phpcs documentation on ignore](https://github.com/squizlabs/PHP_CodeSniffer/wiki/Advanced-Usage#ignoring-parts-of-a-file). If you need to override, be as specific as possible: specify the rule name and favor single line overrides vs ignoring an entire file. If a rule is frequently being ignored, please report it so we can decide if we should: A) change our code style to match the guidelines B) disable the rule completely in `phpcs.xml` or C) continue to use `ignore` on a case-by-case basis. Generally we want to keep the code from becoming littered with lots of ignore overrides.

### js

* Test javascript, all files: `grunt eslint`
* Test one javascript file: `eslint js/example.js`
* Autofix all javascript files: `eslint js/*.js --fix`
* Autofix one javascript file: `eslint js/example.js`
* In special circumstances, one line may be overridden with `alert('foo'); // eslint-disable-line no-alert`. More info in the [eslint user guide](https://eslint.org/docs/user-guide/configuring#disabling-rules-with-inline-comments). See notes on overrides above.

### css

* Test CSS and SASS, all files: `grunt stylelint`
* Test one SCSS file: `npx stylelint css/_123example.scss`
* Autofix all SCSS files: `npx stylelint css/*.scss --fix`
* Autofix one SCSS file: `npx stylelint css/_123example.scss --fix`
* In special circumstances, one line may be overridden with `/* stylelint-disable-line my-style-rule-to-override */`. More info in the [stylelint user guide](https://stylelint.io/user-guide/ignore-code). 

### fix as you work

Recommended VS Code plugins for linting as you work:

* [EditorConfig](https://marketplace.visualstudio.com/items?itemName=EditorConfig.EditorConfig)
* [ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint)
* [phpcs](https://marketplace.visualstudio.com/items?itemName=ikappas.phpcs)
* [phpcbf](https://marketplace.visualstudio.com/items?itemName=persoderlind.vscode-phpcbf)
* [stylelint](https://marketplace.visualstudio.com/items?itemName=stylelint.vscode-stylelint)
