<div align="center">
    <img src="./assets/splint.png" width="300px"/>
    <div>
        <img height="35" alt="Powered: by Black Magic" src="https://forthebadge.com/images/badges/powered-by-black-magic.svg">
        <a href="https://crates.io/crates/splint">
            <img height="35" alt="Crates.io Version" src="https://img.shields.io/crates/v/splint?style=for-the-badge">
        </a>
        <a href="https://github.com/tascord/splint/actions">
            <img height="35" alt="GitHub Actions Workflow Status" src="https://img.shields.io/github/actions/workflow/status/tascord/splint/rust.yml?style=for-the-badge">
        </a>
    </div>
    <br/>
    <h1>Custom Rust Linting</h1>
</div>

### Cli Usage
```bash
# Install Splint
cargo install splint

# Run splint
splint [-r <rules.(json|toml)>] src/**/*.rs # Splint only works on rust files
```

### Integration with Rust Analyzer
Add the following to your `settings.json` file in vscode or equivalent.  
Add `-r <path>` if you have a non-standard rules file (see below).
```jsonc
{
    // ...
    "rust-analyzer.check.overrideCommand": [
        "splint",
        "-qa",
        "**/*.rs"
    ],
    "rust-analyzer.cargo.buildScripts.overrideCommand": [
    
        "splint",
        "-qa",
        "**/*.rs"
    ],
    // ...
}
```


### Rules
The following rule looks for a sequence of `.unwrap()` anywhere in the file.  
You don't need to worry about whitespace, as it uses a parsed stream of tokens from proc_macro2.  
If no rules file is provided, splint will look for a `.splint.(json|toml)` or `splint.(json|toml)` file in the cwd.
```jsonc
// JSON
{
    "rules": {
        "Disallow Unwrap": {
        /* The name of your lint  */                        "name": "Disallow Unwrap",
        /* Reasoning for the lint */                        "description": "`.unwrap()` should be discouraged where possible, as it leads to less than usefull panics.",
        /* (optional) Describe a fix or alternative */      "help": "Favour '?' for Results, or handling with unwrap_or(). At the least give some diagnostics with .expect()",
        /* (optional) Link to more information */           "more": "https://doc.rust-lang.org/std/result/enum.Result.html#method.unwrap",
        /* Whether or not this lint should panic*/          "fails": false,
        /* A replacement for the match */                   "replace": ".expect(\"...\")",
        /* The inclusive range highlighted */               "range": [0, 3], // In this case . -> )
        /* Type/Value matching */                           "pattern": [
        /* Type is one of Punct/Ident/Delim */                  ["Punct", "."],
        /* Where Punctuation handles punctuation, */            ["Ident", "unwrap"],
        /* Delim brackets, and Ident other strings/ */          ["Delim", "("],
        /* Regex in value is defined by surrounding '/' */      ["Delim", ")"]
        /* The value can also be `null` */                  ]
        }
    }
}
```

```toml
# TOML
[rules."Disallow Unwrap"]
name = "Disallow Unwrap"
description = "`.unwrap()` should be discouraged where possible, as it leads to less than usefull panics."
help = "Favour '?' for Results, or handling with unwrap_or(). At the least give some diagnostics with .expect()"
fails = false
range = [0, 3]
pattern = [
    ["Punct", "."],
    ["Ident", "unwrap"],
    ["Delim", "("],
    ["Delim", ")"]
]
```

### Thanks
- #### 🩷 [proc_macro2](https://docs.rs/proc-macro2) & [syn](https://docs.rs/syn) for the brains of parsing
- #### 🩷 [miette](https://docs.rs/miette/) for the gorgeous error handling 