v4.0.3

**Source code:**

The `name` variable is used in the construction of the command string in the `lintFix` function:
```
const lintFix = (name, path) => {
  const cmd = `npx eslint ./${name}/*.{ts,tsx} --quiet --fix`;
  console.warn('Formatting code style ... ');
  console.warn(`Executing $${cmd} ...`);
  try {
    // execSync(`eslint ${path}/*.{ts,tsx} --quiet --fix`);
    execSync(cmd, {   //<-------------Here
      cwd: path,
      stdio: 'inherit',
    });
```
If an attacker can control the name variable and the input is not sanitized, they could inject additional shell commands. For example, if name is set to something malicious like test1; rm -rf ./test, it could result in the execution of a harmful command (rm -rf ./test) after the intended eslint command.
For example:
`node ./cli/index.js create-component --path=./ --name=test; rm -rf /`
