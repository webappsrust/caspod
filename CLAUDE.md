# Caspod - Complete Comprehensive Specification
**Self-Hosted Cloud IDE - Full Technical Specification**

## Project Identity
- **Name**: caspod
- **Organization**: casapps  
- **License**: MIT → LICENSE.md
- **README**: README.md
- **Embedded Licenses**: Added to LICENSE.md with original licenses

---

## 1. Core Architecture

### 1.1 System Components
```rust
struct CaspodIDE {
    // Core runtime
    container_runtime: BuiltinContainerRuntime,
    scheduler: EmbeddedScheduler,
    editor: AdaptiveWebEditor,
    terminal: IntegratedTerminal,
    
    // Feature systems
    features: BuiltinFeatureRegistry,
    templates: TemplateEngine,
    snippets: SnippetEngine,
    language_servers: LanguageServerManager,
    
    // Infrastructure
    config: ConfigManager,
    auth: AuthenticationManager,
    storage: StorageManager,
    networking: NetworkManager,
    security: SecurityManager,
    monitoring: MonitoringSystem,
    
    // Web platform
    web_server: WebServer,
    websocket_server: WebSocketServer,
    static_assets: EmbeddedAssets,
}
```

### 1.2 Installation Paths
```
Global Installation (Preferred):
├── /usr/local/bin/caspod                 # Main binary
├── /etc/caspod/                          # System configuration
├── /var/lib/caspod/                      # Application data
│   ├── database/                         # SQLite databases
│   ├── workspaces/                       # User workspaces
│   ├── containers/                       # Container storage
│   ├── images/                           # Container images
│   ├── templates/                        # File templates
│   └── logs/                             # Application logs
├── /var/log/caspod/                      # System logs
├── /run/caspod/                          # Runtime files
│   ├── sockets/                          # Unix sockets
│   └── pid/                              # Process files
└── /tmp/caspod/                          # Temporary files

User Installation (Fallback):
├── ~/.local/bin/caspod                   # Main binary
├── ~/.config/caspod/                     # User configuration
├── ~/.local/share/caspod/                # Application data
│   ├── database/                         # SQLite databases
│   ├── workspaces/                       # User workspaces
│   ├── containers/                       # Container storage
│   ├── images/                           # Container images
│   ├── templates/                        # File templates
│   └── logs/                             # Application logs
├── ~/.cache/caspod/                      # Cache files
└── /tmp/caspod-{uid}/                    # Temporary files
```

### 1.3 Security Model
```rust
struct SecurityManager {
    privilege_model: PrivilegeModel {
        startup_mode: StartupMode::Root,              // Initial root for setup
        runtime_mode: RuntimeMode::UnprivilegedUser,  // Drop to system user
        system_user_uid: u32,                        // Random 100-998
        system_user_gid: u32,                        // Same as UID
        privilege_drop: PrivilegeDrop::Permanent,    // Cannot regain root
    },
    
    container_security: ContainerSecurity {
        isolation: IsolationLevel::Full,              // Complete container isolation
        resource_limits: ResourceLimits::Strict,     // CPU/memory/disk quotas
        network_isolation: NetworkIsolation::Bridge, // Isolated container network
        filesystem_isolation: true,                  // No host filesystem access
        capabilities: Vec<String>,                   // Minimal Linux capabilities
    },
    
    web_security: WebSecurity {
        csrf_protection: true,                       // CSRF token validation
        xss_protection: true,                        // Content Security Policy
        sql_injection_prevention: true,             // Parameterized queries
        input_validation: ValidationLevel::Strict,   // Validate all inputs
        output_encoding: true,                       // Encode all outputs
        secure_headers: true,                        // Security HTTP headers
    },
}
```

---

## 2. User Interface Specification

### 2.1 Responsive Design System
```css
/* Breakpoint System */
:root {
  --breakpoint-mobile: 320px;
  --breakpoint-tablet: 720px;
  --breakpoint-desktop: 1024px;
  --breakpoint-4k: 2560px;
  --breakpoint-8k: 4096px;
}

/* Layout Widths */
.container {
  /* Mobile: 98% width (1% margins) */
  width: 98%;
  margin: 0 1%;
}

@media (min-width: 720px) {
  .container {
    /* Tablet+: 90% width (5% margins) */
    width: 90%;
    margin: 0 5%;
  }
}

/* Component Scaling */
.component {
  /* Fluid typography */
  font-size: clamp(12px, 1.5vw, 24px);
  
  /* Fluid spacing */
  padding: clamp(8px, 2vw, 32px);
  margin: clamp(4px, 1vw, 16px);
  
  /* Touch targets (minimum 44px) */
  min-height: clamp(44px, 6vw, 64px);
}
```

### 2.2 Adaptive Layout Components
```rust
struct AdaptiveLayout {
    // Screen size detection
    screen_sizes: ScreenSizes {
        mobile: 320..720,        // 1% margins, stack vertically
        tablet: 720..1024,       // 5% margins, limited sidebar
        desktop: 1024..2560,     // 5% margins, full layout
        four_k: 2560..4096,      // 5% margins, enhanced layout
        eight_k: 4096..,         // 5% margins, maximum layout
    },
    
    // Component positioning
    layout_rules: LayoutRules {
        activity_bar: ComponentRule {
            position: Position::Left,
            mobile_position: Position::Bottom,
            visibility: Visibility::Always,
            width: "48px",
            mobile_height: "48px",
        },
        
        sidebar: ComponentRule {
            position: Position::Right,
            mobile_behavior: MobileBehavior::Overlay,
            tablet_behavior: TabletBehavior::Collapsible,
            desktop_behavior: DesktopBehavior::Fixed,
            width: "280px",
            mobile_width: "100%",
        },
        
        editor: ComponentRule {
            position: Position::Center,
            mobile_behavior: MobileBehavior::FullScreen,
            takes_remaining_space: true,
        },
        
        terminal_panel: ComponentRule {
            position: Position::Bottom,
            mobile_behavior: MobileBehavior::Overlay,
            height: "240px",
            mobile_height: "60%",
        },
        
        footer: ComponentRule {
            position: Position::Bottom,
            editor_mode_visibility: Visibility::Hidden,
            other_pages_visibility: Visibility::Visible,
            always_centered: true,
        },
    },
}
```

### 2.3 Editor Gutter System
```rust
struct EditorGutter {
    position: Position::Left,
    columns: Vec<GutterColumn> = vec![
        GutterColumn {
            name: "breakpoints",
            width: "12px",
            purpose: "Debugger breakpoints",
            clickable: true,
            mobile_visibility: Visibility::Hidden,
        },
        GutterColumn {
            name: "line_numbers",
            width: "auto", // Dynamic based on file length
            min_width: "40px",
            max_width: "80px",
            purpose: "Line numbers",
            clickable: true,
            mobile_width: "30px",
        },
        GutterColumn {
            name: "git_gutter",
            width: "4px",
            purpose: "Git diff indicators",
            colors: GutterColors {
                added: "#50fa7b",       // Green
                modified: "#8be9fd",    // Cyan
                deleted: "#ff5555",     // Red
            },
            clickable: true,
        },
        GutterColumn {
            name: "folding",
            width: "12px",
            purpose: "Code folding controls",
            clickable: true,
            mobile_touch_target: "24px",
        },
        GutterColumn {
            name: "gremlins",
            width: "4px",
            purpose: "Problematic character warnings",
            hover_tooltips: true,
            mobile_visibility: Visibility::StatusBar,
        },
    ],
}
```

### 2.4 Theme System
```rust
struct ThemeSystem {
    default_theme: "dark",
    available_themes: Vec<Theme> = vec![
        Theme {
            name: "dark",
            description: "Dark theme (default)",
            colors: ThemeColors {
                // Background colors
                bg_primary: "#282a36",
                bg_secondary: "#44475a",
                bg_tertiary: "#6272a4",
                bg_elevated: "#3a3c4e",
                
                // Text colors
                text_primary: "#f8f8f2",
                text_secondary: "#6272a4",
                text_muted: "#44475a",
                text_disabled: "#2f313d",
                
                // Accent colors
                accent_primary: "#bd93f9",      // Purple
                accent_secondary: "#ff79c6",    // Pink
                accent_tertiary: "#8be9fd",     // Cyan
                
                // Status colors
                success: "#50fa7b",             // Green
                warning: "#f1fa8c",             // Yellow
                error: "#ff5555",               // Red
                info: "#8be9fd",                // Cyan
                
                // UI elements
                border: "#44475a",
                shadow: "rgba(0, 0, 0, 0.3)",
                focus: "#bd93f9",
                selection: "#44475a",
                
                // Syntax highlighting
                syntax_comment: "#6272a4",
                syntax_string: "#f1fa8c",
                syntax_keyword: "#ff79c6",
                syntax_function: "#50fa7b",
                syntax_variable: "#f8f8f2",
                syntax_type: "#8be9fd",
                syntax_number: "#bd93f9",
                syntax_operator: "#ff79c6",
            },
        },
        
        Theme {
            name: "light",
            description: "Light theme",
            colors: ThemeColors {
                // Background colors
                bg_primary: "#ffffff",
                bg_secondary: "#f8f9fa",
                bg_tertiary: "#e9ecef",
                bg_elevated: "#ffffff",
                
                // Text colors
                text_primary: "#212529",
                text_secondary: "#6c757d",
                text_muted: "#adb5bd",
                text_disabled: "#dee2e6",
                
                // Accent colors
                accent_primary: "#007bff",      // Blue
                accent_secondary: "#6f42c1",    // Purple
                accent_tertiary: "#17a2b8",     // Teal
                
                // Status colors
                success: "#28a745",             // Green
                warning: "#ffc107",             // Amber
                error: "#dc3545",               // Red
                info: "#17a2b8",                // Teal
                
                // UI elements
                border: "#dee2e6",
                shadow: "rgba(0, 0, 0, 0.1)",
                focus: "#007bff",
                selection: "#e3f2fd",
                
                // Syntax highlighting
                syntax_comment: "#6a737d",
                syntax_string: "#032f62",
                syntax_keyword: "#d73a49",
                syntax_function: "#6f42c1",
                syntax_variable: "#24292e",
                syntax_type: "#005cc5",
                syntax_number: "#005cc5",
                syntax_operator: "#d73a49",
            },
        },
        
        Theme {
            name: "base16",
            description: "Base16 theme variant",
            // Additional theme colors...
        },
    ],
}
```

---

## 3. Built-in Feature Registry

### 3.1 Complete Feature Set
```rust
struct BuiltinFeatureRegistry {
    // Core editor features
    editor_features: EditorFeatures {
        // Text editing
        auto_pairs: AutoPairsConfig {
            enabled: true,
            pairs: HashMap::from([
                ("(", ")"), ("[", "]"), ("{", "}"),
                ("\"", "\""), ("'", "'"), ("`", "`"),
            ]),
            language_specific: HashMap::from([
                ("html", vec![("<", ">")]),
                ("xml", vec![("<", ">")]),
            ]),
        },
        
        multiple_cursors: MultipleCursorsConfig {
            enabled: true,
            max_cursors: 1000,
            key_binding: "Ctrl+D",
            select_all_binding: "Ctrl+Shift+L",
        },
        
        smart_commenting: CommentingConfig {
            enabled: true,
            toggle_line_comment: "Ctrl+/",
            toggle_block_comment: "Ctrl+Shift+/",
            language_comments: HashMap::from([
                ("javascript", LineComment("//"), BlockComment("/*", "*/")),
                ("python", LineComment("#"), BlockComment("\"\"\"", "\"\"\"")),
                ("rust", LineComment("//"), BlockComment("/*", "*/")),
                ("html", LineComment::None, BlockComment("<!--", "-->")),
                ("css", LineComment::None, BlockComment("/*", "*/")),
                ("bash", LineComment("#"), BlockComment::None),
            ]),
        },
        
        rainbow_brackets: RainbowBracketsConfig {
            enabled: true,
            colors: vec![
                "#ff79c6", "#8be9fd", "#50fa7b", 
                "#f1fa8c", "#bd93f9", "#ffb86c"
            ],
            bracket_types: vec!["()", "[]", "{}", "<>"],
        },
        
        auto_rename_tag: AutoRenameTagConfig {
            enabled: true,
            file_extensions: vec!["html", "xml", "jsx", "tsx", "vue"],
            mirror_cursor: true,
        },
        
        // Visual enhancements
        indent_guides: IndentGuideConfig {
            enabled: true,
            style: IndentGuideStyle::Rainbow,
            colors: vec![
                "rgba(255, 121, 198, 0.3)",
                "rgba(139, 233, 253, 0.3)", 
                "rgba(80, 250, 123, 0.3)",
                "rgba(241, 250, 140, 0.3)",
            ],
        },
        
        color_preview: ColorPreviewConfig {
            enabled: true,
            file_extensions: vec!["css", "scss", "less", "html", "vue"],
            formats: vec!["hex", "rgb", "rgba", "hsl", "hsla"],
            inline_preview: true,
        },
        
        minimap: MinimapConfig {
            enabled: false,  // Disabled by default for mobile
            size: MinimapSize::Small,
            show_slider: true,
            render_characters: false,
        },
        
        // Code intelligence
        intellisense: IntelliSenseConfig {
            enabled: true,
            trigger_characters: vec![".", "->", "::", "("],
            auto_trigger_delay: 300, // milliseconds
            show_documentation: true,
            show_examples: true,
        },
        
        parameter_hints: ParameterHintsConfig {
            enabled: true,
            trigger_characters: vec!["(", ","],
            show_documentation: true,
        },
        
        go_to_definition: GoToDefinitionConfig {
            enabled: true,
            open_in_peek: false,
            key_binding: "F12",
            ctrl_click: true,
        },
        
        find_references: FindReferencesConfig {
            enabled: true,
            key_binding: "Shift+F12",
            show_peek_view: true,
            include_declaration: true,
        },
        
        // Formatting
        format_on_save: true,
        format_on_paste: true,
        format_on_type: true,
        trim_whitespace: true,
        insert_final_newline: true,
        trim_final_newlines: true,
        
        // Editor behavior
        auto_save: AutoSaveMode::OnWindowChange,
        tab_size: 2,
        insert_spaces: true,
        word_wrap: WordWrapMode::WordWrapColumn,
        word_wrap_column: 160,
        line_numbers: LineNumberMode::On,
        render_whitespace: WhitespaceMode::Boundary,
        cursor_blinking: CursorBlinking::Expand,
        cursor_smooth_caret_animation: true,
        linked_editing: true,
        bracket_pair_colorization: true,
        guides_bracket_pairs: BracketGuideMode::Active,
    },
    
    // Git integration
    git_features: GitFeatures {
        git_gutter: GitGutterConfig {
            enabled: true,
            show_added_lines: true,
            show_modified_lines: true,
            show_deleted_lines: true,
            update_delay: 500, // milliseconds
            colors: GitGutterColors {
                added: "#50fa7b",
                modified: "#8be9fd", 
                deleted: "#ff5555",
            },
        },
        
        git_blame: GitBlameConfig {
            enabled: true,
            show_inline: false,
            show_on_hover: true,
            format: "{author} • {date} • {message}",
            key_binding: "Ctrl+Shift+B",
        },
        
        git_staging: GitStagingConfig {
            enabled: true,
            interactive_staging: true,
            chunk_staging: true,
            line_staging: true,
        },
        
        git_diff: GitDiffConfig {
            enabled: true,
            side_by_side: false,
            ignore_whitespace: false,
            context_lines: 3,
        },
        
        git_status: GitStatusConfig {
            enabled: true,
            show_in_file_tree: true,
            show_in_status_bar: true,
            auto_refresh: true,
            refresh_interval: 5000, // milliseconds
        },
    },
    
    // File management
    file_management: FileManagementFeatures {
        file_tree: FileTreeConfig {
            enabled: true,
            position: Position::Right,
            default_width: 280,
            show_git_status: true,
            show_file_icons: true,
            compact_folders: true,
            auto_reveal: true,
            exclude_patterns: vec![
                "node_modules", ".git", "target", 
                "dist", "build", ".next", ".cache"
            ],
        },
        
        file_icons: FileIconConfig {
            enabled: true,
            icon_set: "material",
            show_folder_icons: true,
            show_file_icons: true,
            custom_icons: HashMap::new(),
        },
        
        fuzzy_finder: FuzzyFinderConfig {
            enabled: true,
            key_binding: "Ctrl+P",
            search_files: true,
            search_symbols: true,
            search_text: true,
            max_results: 100,
            case_sensitive: false,
        },
        
        quick_open: QuickOpenConfig {
            enabled: true,
            recent_files_count: 20,
            show_recent_workspaces: true,
            key_binding: "Ctrl+E",
        },
        
        breadcrumbs: BreadcrumbConfig {
            enabled: true,
            show_file_path: true,
            show_symbols: true,
            max_items: 10,
        },
    },
    
    // Search features
    search_features: SearchFeatures {
        text_search: TextSearchConfig {
            enabled: true,
            key_binding: "Ctrl+Shift+F",
            case_sensitive: false,
            whole_word: false,
            regex: false,
            include_ignored_files: false,
            max_results: 10000,
        },
        
        replace: ReplaceConfig {
            enabled: true,
            key_binding: "Ctrl+Shift+H",
            preserve_case: false,
            confirm_each: false,
        },
        
        symbol_search: SymbolSearchConfig {
            enabled: true,
            key_binding: "Ctrl+T",
            workspace_symbols: true,
            document_symbols: true,
        },
    },
    
    // Language support
    language_support: LanguageSupport {
        language_servers: HashMap::from([
            ("javascript", LanguageServerConfig {
                name: "typescript-language-server",
                command: "typescript-language-server",
                args: vec!["--stdio"],
                initialization_options: serde_json::json!({}),
                settings: serde_json::json!({
                    "typescript": {
                        "preferences": {
                            "quoteStyle": "single"
                        }
                    }
                }),
                file_extensions: vec!["js", "jsx", "mjs"],
                auto_install: true,
            }),
            
            ("typescript", LanguageServerConfig {
                name: "typescript-language-server",
                command: "typescript-language-server", 
                args: vec!["--stdio"],
                initialization_options: serde_json::json!({}),
                settings: serde_json::json!({
                    "typescript": {
                        "preferences": {
                            "quoteStyle": "single"
                        }
                    }
                }),
                file_extensions: vec!["ts", "tsx"],
                auto_install: true,
            }),
            
            ("python", LanguageServerConfig {
                name: "pylsp",
                command: "pylsp",
                args: vec![],
                initialization_options: serde_json::json!({}),
                settings: serde_json::json!({
                    "pylsp": {
                        "plugins": {
                            "pycodestyle": {"enabled": true},
                            "pyflakes": {"enabled": true},
                            "autopep8": {"enabled": true}
                        }
                    }
                }),
                file_extensions: vec!["py", "pyi"],
                auto_install: true,
            }),
            
            ("rust", LanguageServerConfig {
                name: "rust-analyzer",
                command: "rust-analyzer",
                args: vec![],
                initialization_options: serde_json::json!({}),
                settings: serde_json::json!({
                    "rust-analyzer": {
                        "checkOnSave": {
                            "command": "clippy"
                        }
                    }
                }),
                file_extensions: vec!["rs"],
                auto_install: true,
            }),
            
            ("go", LanguageServerConfig {
                name: "gopls",
                command: "gopls",
                args: vec![],
                initialization_options: serde_json::json!({}),
                settings: serde_json::json!({}),
                file_extensions: vec!["go"],
                auto_install: true,
            }),
            
            ("java", LanguageServerConfig {
                name: "jdtls",
                command: "jdtls",
                args: vec![],
                initialization_options: serde_json::json!({}),
                settings: serde_json::json!({}),
                file_extensions: vec!["java"],
                auto_install: true,
            }),
            
            ("php", LanguageServerConfig {
                name: "intelephense",
                command: "intelephense",
                args: vec!["--stdio"],
                initialization_options: serde_json::json!({}),
                settings: serde_json::json!({}),
                file_extensions: vec!["php"],
                auto_install: true,
            }),
            
            ("html", LanguageServerConfig {
                name: "vscode-html-language-server",
                command: "vscode-html-language-server",
                args: vec!["--stdio"],
                initialization_options: serde_json::json!({}),
                settings: serde_json::json!({}),
                file_extensions: vec!["html", "htm"],
                auto_install: true,
            }),
            
            ("css", LanguageServerConfig {
                name: "vscode-css-language-server",
                command: "vscode-css-language-server",
                args: vec!["--stdio"],
                initialization_options: serde_json::json!({}),
                settings: serde_json::json!({}),
                file_extensions: vec!["css", "scss", "less"],
                auto_install: true,
            }),
            
            ("json", LanguageServerConfig {
                name: "vscode-json-language-server",
                command: "vscode-json-language-server",
                args: vec!["--stdio"],
                initialization_options: serde_json::json!({}),
                settings: serde_json::json!({}),
                file_extensions: vec!["json", "jsonc"],
                auto_install: true,
            }),
            
            ("yaml", LanguageServerConfig {
                name: "yaml-language-server",
                command: "yaml-language-server",
                args: vec!["--stdio"],
                initialization_options: serde_json::json!({}),
                settings: serde_json::json!({}),
                file_extensions: vec!["yaml", "yml"],
                auto_install: true,
            }),
            
            ("markdown", LanguageServerConfig {
                name: "marksman",
                command: "marksman",
                args: vec!["server"],
                initialization_options: serde_json::json!({}),
                settings: serde_json::json!({}),
                file_extensions: vec!["md", "markdown"],
                auto_install: true,
            }),
            
            ("dockerfile", LanguageServerConfig {
                name: "dockerfile-language-server",
                command: "dockerfile-language-server-nodejs",
                args: vec!["--stdio"],
                initialization_options: serde_json::json!({}),
                settings: serde_json::json!({}),
                file_extensions: vec!["dockerfile"],
                auto_install: true,
            }),
            
            ("bash", LanguageServerConfig {
                name: "bash-language-server",
                command: "bash-language-server",
                args: vec!["start"],
                initialization_options: serde_json::json!({}),
                settings: serde_json::json!({}),
                file_extensions: vec!["sh", "bash", "zsh"],
                auto_install: true,
            }),
            
            ("sql", LanguageServerConfig {
                name: "sqls",
                command: "sqls",
                args: vec![],
                initialization_options: serde_json::json!({}),
                settings: serde_json::json!({}),
                file_extensions: vec!["sql"],
                auto_install: true,
            }),
            
            ("c", LanguageServerConfig {
                name: "clangd",
                command: "clangd",
                args: vec![],
                initialization_options: serde_json::json!({}),
                settings: serde_json::json!({}),
                file_extensions: vec!["c", "h"],
                auto_install: true,
            }),
            
            ("cpp", LanguageServerConfig {
                name: "clangd",
                command: "clangd",
                args: vec![],
                initialization_options: serde_json::json!({}),
                settings: serde_json::json!({}),
                file_extensions: vec!["cpp", "cxx", "cc", "hpp", "hxx"],
                auto_install: true,
            }),
        ]),
        
        formatters: HashMap::from([
            ("javascript", FormatterConfig {
                name: "prettier",
                command: "prettier",
                args: vec!["--stdin-filepath", "{file}"],
                settings: serde_json::json!({
                    "singleQuote": true,
                    "tabWidth": 2,
                    "semi": true,
                    "trailingComma": "es5"
                }),
            }),
            
            ("typescript", FormatterConfig {
                name: "prettier",
                command: "prettier",
                args: vec!["--stdin-filepath", "{file}"],
                settings: serde_json::json!({
                    "singleQuote": true,
                    "tabWidth": 2,
                    "semi": true,
                    "trailingComma": "es5"
                }),
            }),
            
            ("python", FormatterConfig {
                name: "black",
                command: "black",
                args: vec!["--stdin-filename", "{file}", "-"],
                settings: serde_json::json!({
                    "line_length": 88
                }),
            }),
            
            ("rust", FormatterConfig {
                name: "rustfmt",
                command: "rustfmt",
                args: vec!["--stdin-filename", "{file}"],
                settings: serde_json::json!({}),
            }),
            
            ("go", FormatterConfig {
                name: "gofmt",
                command: "gofmt",
                args: vec![],
                settings: serde_json::json!({}),
            }),
            
            ("java", FormatterConfig {
                name: "google-java-format",
                command: "google-java-format",
                args: vec!["-"],
                settings: serde_json::json!({}),
            }),
            
            ("php", FormatterConfig {
                name: "php-cs-fixer",
                command: "php-cs-fixer",
                args: vec!["fix", "--stdin-filename={file}", "-"],
                settings: serde_json::json!({}),
            }),
            
            ("html", FormatterConfig {
                name: "prettier",
                command: "prettier",
                args: vec!["--parser", "html"],
                settings: serde_json::json!({
                    "tabWidth": 2,
                    "htmlWhitespaceSensitivity": "css"
                }),
            }),
            
            ("css", FormatterConfig {
                name: "prettier",
                command: "prettier",
                args: vec!["--parser", "css"],
                settings: serde_json::json!({
                    "tabWidth": 2
                }),
            }),
            
            ("json", FormatterConfig {
                name: "prettier",
                command: "prettier",
                args: vec!["--parser", "json"],
                settings: serde_json::json!({
                    "tabWidth": 2
                }),
            }),
            
            ("yaml", FormatterConfig {
                name: "prettier",
                command: "prettier",
                args: vec!["--parser", "yaml"],
                settings: serde_json::json!({
                    "tabWidth": 2
                }),
            }),
            
            ("markdown", FormatterConfig {
                name: "prettier",
                command: "prettier",
                args: vec!["--parser", "markdown"],
                settings: serde_json::json!({
                    "tabWidth": 2,
                    "proseWrap": "preserve"
                }),
            }),
            
            ("bash", FormatterConfig {
                name: "shfmt",
                command: "shfmt",
                args: vec!["-i", "2", "-ci"],
                settings: serde_json::json!({}),
            }),
            
            ("sql", FormatterConfig {
                name: "sql-formatter",
                command: "sql-formatter",
                args: vec![],
                settings: serde_json::json!({
                    "language": "sql",
                    "tabWidth": 2
                }),
            }),
        ]),
        
        linters: HashMap::from([
            ("javascript", LinterConfig {
                name: "eslint",
                command: "eslint",
                args: vec!["--format", "json", "--stdin", "--stdin-filename", "{file}"],
                settings: serde_json::json!({}),
            }),
            
            ("typescript", LinterConfig {
                name: "eslint",
                command: "eslint",
                args: vec!["--format", "json", "--stdin", "--stdin-filename", "{file}"],
                settings: serde_json::json!({}),
            }),
            
            ("python", LinterConfig {
                name: "flake8",
                command: "flake8",
                args: vec!["--format", "json", "-"],
                settings: serde_json::json!({}),
            }),
            
            ("rust", LinterConfig {
                name: "clippy",
                command: "cargo",
                args: vec!["clippy", "--message-format", "json"],
                settings: serde_json::json!({}),
            }),
            
            ("bash", LinterConfig {
                name: "shellcheck",
                command: "shellcheck",
                args: vec!["--format", "json", "-"],
                settings: serde_json::json!({}),
            }),
            
            ("dockerfile", LinterConfig {
                name: "hadolint",
                command: "hadolint",
                args: vec!["--format", "json", "-"],
                settings: serde_json::json!({}),
            }),
        ]),
    },
    
    // Terminal features
    terminal_features: TerminalFeatures {
        integrated_terminal: TerminalConfig {
            enabled: true,
            position: Position::Bottom,
            default_height: 240,
            mobile_height_percentage: 60,
            font_family: "monospace",
            font_size: 12,
            scrollback: 1000,
            cursor_blink: true,
            cursor_style: CursorStyle::Block,
            bell_sound: false,
            copy_on_select: false,
        },
        
        multiple_terminals: MultipleTerminalConfig {
            enabled: true,
            max_terminals: 10,
            tab_switching: true,
            split_terminals: true,
        },
        
        shell_integration: ShellIntegrationConfig {
            enabled: true,
            auto_detect_shell: true,
            shell_priority: vec![
                "/usr/bin/bash",
                "/bin/bash", 
                "/bin/zsh",
                "/bin/sh"
            ],
            environment_variables: HashMap::from([
                ("TERM", "xterm-256color"),
                ("COLORTERM", "truecolor"),
            ]),
        },
        
        terminal_links: TerminalLinkConfig {
            enabled: true,
            detect_urls: true,
            detect_file_paths: true,
            click_to_open: true,
        },
    },
    
    // Workflow features
    workflow_features: WorkflowFeatures {
        live_server: LiveServerConfig {
            enabled: true,
            default_port: 3000,
            auto_open_browser: false,
            hot_reload: true,
            cors_enabled: true,
            fallback_to_index: true,
            watch_extensions: vec!["html", "css", "js", "ts"],
        },
        
        task_runner: TaskRunnerConfig {
            enabled: true,
            auto_detect_tasks: true,
            task_sources: vec![
                TaskSource::PackageJson,
                TaskSource::Makefile,
                TaskSource::CargoToml,
                TaskSource::Custom,
            ],
        },
        
        debugging: DebuggingConfig {
            enabled: true,
            languages: vec![
                "javascript", "typescript", "python", 
                "rust", "go", "java", "c", "cpp"
            ],
            breakpoint_support: true,
            variable_inspection: true,
            call_stack: true,
            watch_expressions: true,
        },
        
        project_management: ProjectManagementConfig {
            enabled: true,
            auto_detect_project_type: true,
            workspace_folders: true,
            recent_workspaces: 20,
        },
    },
    
    // Productivity features
    productivity_features: ProductivityFeatures {
        emmet: EmmetConfig {
            enabled: true,
            languages: vec!["html", "css", "scss", "jsx", "tsx", "vue"],
            trigger_expansion_on_tab: true,
            show_suggestions: true,
        },
        
        markdown_preview: MarkdownPreviewConfig {
            enabled: true,
            live_preview: true,
            side_by_side: true,
            math_support: true,
            mermaid_diagrams: true,
            syntax_highlighting: true,
        },
        
        spell_checker: SpellCheckerConfig {
            enabled: true,
            languages: vec!["en-US"],
            check_comments: true,
            check_strings: true,
            personal_dictionary: true,
            file_types: vec![
                "markdown", "text", "plaintext", "html", "latex"
            ],
        },
        
        word_counter: WordCounterConfig {
            enabled: true,
            show_in_status_bar: true,
            file_types: vec!["markdown", "text", "plaintext"],
        },
        
        focus_mode: FocusModeConfig {
            enabled: true,
            dim_non_focused_lines: true,
            highlight_current_line: true,
            zen_mode: true,
        },
    },
}
```

### 3.2 Snippet System
```rust
struct SnippetEngine {
    global_snippets: HashMap<String, Snippet>,
    language_snippets: HashMap<String, HashMap<String, Snippet>>,
    user_snippets: HashMap<String, Snippet>,
    snippet_processor: SnippetProcessor,
}

// Global snippets (language-agnostic)
const GLOBAL_SNIPPETS: &[(&str, &str, &str)] = &[
    // Date/Time snippets
    ("dte", "Date (MM/DD/YYYY)", "{{DATE_MM_DD_YYYY}}"),
    ("tme", "Time (HH:MM)", "{{TIME_HH_MM}}"),
    ("now", "Full timestamp", "{{FULL_TIMESTAMP}}"),
    ("today", "Today's date", "{{TODAY}}"),
    ("time", "Current time", "{{TIME}}"),
    ("timestamp", "Complete timestamp", "{{TIMESTAMP}}"),
    ("version", "Version timestamp", "{{VERSION_TIMESTAMP}}-git"),
    ("setver", "Version number", "{{SETVER}}"),
    
    // Attention markers
    ("todo", "TODO comment", "TODO: ${1:description}"),
    ("fixme", "FIXME comment", "FIXME: ${1:description}"),
    ("note", "NOTE comment", "NOTE: ${1:description}"),
    ("hack", "HACK comment", "HACK: ${1:description}"),
    ("bug", "BUG comment", "BUG: ${1:description}"),
    ("wip", "Work in progress", "WIP: ${1:description}"),
    ("xxx", "XXX comment", "XXX: ${1:description}"),
    
    // File headers
    ("header", "Complete file header", COMPREHENSIVE_HEADER_TEMPLATE),
    ("hdr", "Header alias", COMPREHENSIVE_HEADER_TEMPLATE),
    ("fileheader", "File header", COMPREHENSIVE_HEADER_TEMPLATE),
    
    // Visual elements
    ("box", "Simple box", "┌─${1:title}─┐\n│ ${2:content} │\n└─${1/./─/g}─┘"),
    ("box2", "Dynamic box", "┌${{BOX_TOP}}┐\n│ ${1:content} │\n└${{BOX_BOTTOM}}┘"),
    ("line", "Separator line", "# ${1:- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -}"),
    ("comment", "Section comment", "\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\\n\" => ${1:Section}\\n\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\""),
    
    // Templates
    ("blogpost", "Blog post frontmatter", BLOG_POST_TEMPLATE),
    ("license", "License header", "{{LICENSE_FILE}}"),
    ("author", "Author info", "{{NAME}} <{{EMAIL}}>"),
    ("contact", "Contact info", "Contact: {{EMAIL}}"),
    ("copyright", "Copyright notice", "{{COPYRIGHT}} {{YEAR}}, {{COMPANY}}"),
];

// Language-specific snippets
const LANGUAGE_SNIPPETS: &[(&str, &[(&str, &str, &str)])] = &[
    ("javascript", &[
        ("fn", "Function", "function ${1:name}(${2:params}) {\n\t${0}\n}"),
        ("arrow", "Arrow function", "const ${1:name} = (${2:params}) => {\n\t${0}\n}"),
        ("class", "Class", "class ${1:Name} {\n\tconstructor(${2:params}) {\n\t\t${0}\n\t}\n}"),
        ("if", "If statement", "if (${1:condition}) {\n\t${0}\n}"),
        ("for", "For loop", "for (let ${1:i} = 0; ${1:i} < ${2:length}; ${1:i}++) {\n\t${0}\n}"),
        ("foreach", "For each", "for (const ${1:item} of ${2:array}) {\n\t${0}\n}"),
        ("try", "Try catch", "try {\n\t${1}\n} catch (${2:error}) {\n\t${0}\n}"),
        ("log", "Console log", "console.log(${1:'Hello World'})"),
        ("import", "Import", "import ${1:name} from '${2:module}'"),
        ("export", "Export", "export ${1:default} ${2:name}"),
    ]),
    
    ("typescript", &[
        ("interface", "Interface", "interface ${1:Name} {\n\t${0}\n}"),
        ("type", "Type alias", "type ${1:Name} = ${0}"),
        ("enum", "Enum", "enum ${1:Name} {\n\t${0}\n}"),
        ("generic", "Generic function", "function ${1:name}<${2:T}>(${3:param}: ${2:T}): ${4:T} {\n\t${0}\n}"),
    ]),
    
    ("python", &[
        ("def", "Function", "def ${1:name}(${2:params}):\n\t\"\"\"${3:docstring}\"\"\"\n\t${0}"),
        ("class", "Class", "class ${1:Name}:\n\t\"\"\"${2:docstring}\"\"\"\n\t\n\tdef __init__(self, ${3:params}):\n\t\t${0}"),
        ("if", "If statement", "if ${1:condition}:\n\t${0}"),
        ("for", "For loop", "for ${1:item} in ${2:iterable}:\n\t${0}"),
        ("while", "While loop", "while ${1:condition}:\n\t${0}"),
        ("try", "Try except", "try:\n\t${1}\nexcept ${2:Exception} as ${3:e}:\n\t${0}"),
        ("with", "With statement", "with ${1:expression} as ${2:variable}:\n\t${0}"),
        ("main", "Main guard", "if __name__ == '__main__':\n\t${0}"),
        ("import", "Import", "import ${1:module}"),
        ("from", "From import", "from ${1:module} import ${2:name}"),
    ]),
    
    ("rust", &[
        ("fn", "Function", "fn ${1:name}(${2:params}) -> ${3:ReturnType} {\n\t${0}\n}"),
        ("struct", "Struct", "struct ${1:Name} {\n\t${0}\n}"),
        ("enum", "Enum", "enum ${1:Name} {\n\t${0}\n}"),
        ("impl", "Implementation", "impl ${1:Type} {\n\t${0}\n}"),
        ("trait", "Trait", "trait ${1:Name} {\n\t${0}\n}"),
        ("match", "Match expression", "match ${1:expression} {\n\t${2:pattern} => ${3:result},\n\t_ => ${0}\n}"),
        ("if", "If expression", "if ${1:condition} {\n\t${2}\n} else {\n\t${0}\n}"),
        ("for", "For loop", "for ${1:item} in ${2:iterator} {\n\t${0}\n}"),
        ("while", "While loop", "while ${1:condition} {\n\t${0}\n}"),
        ("macro", "Macro", "macro_rules! ${1:name} {\n\t(${2:pattern}) => {\n\t\t${0}\n\t};\n}"),
        ("test", "Test function", "#[test]\nfn ${1:test_name}() {\n\t${0}\n}"),
        ("main", "Main function", "fn main() {\n\t${0}\n}"),
    ]),
    
    ("go", &[
        ("func", "Function", "func ${1:name}(${2:params}) ${3:returnType} {\n\t${0}\n}"),
        ("method", "Method", "func (${1:receiver} ${2:Type}) ${3:name}(${4:params}) ${5:returnType} {\n\t${0}\n}"),
        ("struct", "Struct", "type ${1:Name} struct {\n\t${0}\n}"),
        ("interface", "Interface", "type ${1:Name} interface {\n\t${0}\n}"),
        ("if", "If statement", "if ${1:condition} {\n\t${0}\n}"),
        ("for", "For loop", "for ${1:i} := 0; ${1:i} < ${2:length}; ${1:i}++ {\n\t${0}\n}"),
        ("range", "Range loop", "for ${1:key}, ${2:value} := range ${3:collection} {\n\t${0}\n}"),
        ("switch", "Switch statement", "switch ${1:expression} {\ncase ${2:value}:\n\t${3}\ndefault:\n\t${0}\n}"),
        ("defer", "Defer statement", "defer ${0}"),
        ("go", "Goroutine", "go func() {\n\t${0}\n}()"),
        ("chan", "Channel", "make(chan ${1:type})"),
        ("select", "Select statement", "select {\ncase ${1:channel}:\n\t${2}\ndefault:\n\t${0}\n}"),
    ]),
    
    ("java", &[
        ("class", "Class", "public class ${1:Name} {\n\t${0}\n}"),
        ("method", "Method", "${1:public} ${2:void} ${3:name}(${4:params}) {\n\t${0}\n}"),
        ("main", "Main method", "public static void main(String[] args) {\n\t${0}\n}"),
        ("if", "If statement", "if (${1:condition}) {\n\t${0}\n}"),
        ("for", "For loop", "for (int ${1:i} = 0; ${1:i} < ${2:length}; ${1:i}++) {\n\t${0}\n}"),
        ("foreach", "Enhanced for", "for (${1:Type} ${2:item} : ${3:collection}) {\n\t${0}\n}"),
        ("while", "While loop", "while (${1:condition}) {\n\t${0}\n}"),
        ("try", "Try catch", "try {\n\t${1}\n} catch (${2:Exception} ${3:e}) {\n\t${0}\n}"),
        ("switch", "Switch statement", "switch (${1:expression}) {\n\tcase ${2:value}:\n\t\t${3}\n\t\tbreak;\n\tdefault:\n\t\t${0}\n}"),
        ("interface", "Interface", "public interface ${1:Name} {\n\t${0}\n}"),
    ]),
    
    ("html", &[
        ("html5", "HTML5 template", "<!DOCTYPE html>\n<html lang=\"en\">\n<head>\n\t<meta charset=\"UTF-8\">\n\t<meta name=\"viewport\" content=\"width=device-width, initial-scale=1.0\">\n\t<title>${1:Document}</title>\n</head>\n<body>\n\t${0}\n</body>\n</html>"),
        ("div", "Div element", "<div${1: class=\"${2:className}\"}>\n\t${0}\n</div>"),
        ("span", "Span element", "<span${1: class=\"${2:className}\"}>${0}</span>"),
        ("a", "Anchor tag", "<a href=\"${1:url}\"${2: target=\"_blank\"}>${3:text}</a>"),
        ("img", "Image tag", "<img src=\"${1:url}\" alt=\"${2:description}\"${3: width=\"${4}\" height=\"${5}\"}>"),
        ("input", "Input field", "<input type=\"${1:text}\" name=\"${2:name}\" value=\"${3:value}\"${4: placeholder=\"${5:placeholder}\"}>"),
        ("button", "Button", "<button type=\"${1:button}\"${2: class=\"${3:className}\"}>${4:text}</button>"),
        ("form", "Form", "<form action=\"${1:url}\" method=\"${2:post}\">\n\t${0}\n</form>"),
        ("table", "Table", "<table${1: class=\"${2:className}\"}>\n\t<thead>\n\t\t<tr>\n\t\t\t<th>${3:Header}</th>\n\t\t</tr>\n\t</thead>\n\t<tbody>\n\t\t<tr>\n\t\t\t<td>${4:Data}</td>\n\t\t</tr>\n\t</tbody>\n</table>"),
        ("script", "Script tag", "<script${1: src=\"${2:url}\"}>\n\t${0}\n</script>"),
        ("style", "Style tag", "<style>\n\t${0}\n</style>"),
        ("link", "Link tag", "<link rel=\"${1:stylesheet}\" href=\"${2:url}\">"),
    ]),
    
    ("css", &[
        ("class", "CSS class", ".${1:className} {\n\t${0}\n}"),
        ("id", "CSS ID", "#${1:idName} {\n\t${0}\n}"),
        ("media", "Media query", "@media (${1:min-width: 768px}) {\n\t${0}\n}"),
        ("keyframes", "Keyframes animation", "@keyframes ${1:animationName} {\n\t0% {\n\t\t${2}\n\t}\n\t100% {\n\t\t${0}\n\t}\n}"),
        ("flex", "Flexbox", "display: flex;\njustify-content: ${1:center};\nalign-items: ${2:center};"),
        ("grid", "CSS Grid", "display: grid;\ngrid-template-columns: ${1:1fr 1fr};\ngap: ${2:1rem};"),
        ("transition", "Transition", "transition: ${1:all} ${2:0.3s} ${3:ease};"),
        ("transform", "Transform", "transform: ${1:translateX(${2:0px})};"),
        ("gradient", "Linear gradient", "background: linear-gradient(${1:to right}, ${2:#ff0000}, ${3:#0000ff});"),
        ("shadow", "Box shadow", "box-shadow: ${1:0 2px 4px rgba(0, 0, 0, 0.1)};"),
    ]),
    
    ("markdown", &[
        ("h1", "Heading 1", "# ${1:Heading}"),
        ("h2", "Heading 2", "## ${1:Heading}"),
        ("h3", "Heading 3", "### ${1:Heading}"),
        ("bold", "Bold text", "**${1:text}**"),
        ("italic", "Italic text", "*${1:text}*"),
        ("code", "Inline code", "`${1:code}`"),
        ("codeblock", "Code block", "```${1:language}\n${2:code}\n```"),
        ("link", "Link", "[${1:text}](${2:url})"),
        ("image", "Image", "![${1:alt text}](${2:url})"),
        ("list", "Unordered list", "- ${1:item 1}\n- ${2:item 2}\n- ${0:item 3}"),
        ("olist", "Ordered list", "1. ${1:item 1}\n2. ${2:item 2}\n3. ${0:item 3}"),
        ("table", "Table", "| ${1:Header 1} | ${2:Header 2} |\n|-------------|-------------|\n| ${3:Cell 1}  | ${4:Cell 2}  |"),
        ("quote", "Blockquote", "> ${1:quote}"),
        ("hr", "Horizontal rule", "---"),
    ]),
    
    ("sql", &[
        ("select", "Select statement", "SELECT ${1:columns}\nFROM ${2:table}\nWHERE ${3:condition};"),
        ("insert", "Insert statement", "INSERT INTO ${1:table} (${2:columns})\nVALUES (${3:values});"),
        ("update", "Update statement", "UPDATE ${1:table}\nSET ${2:column} = ${3:value}\nWHERE ${4:condition};"),
        ("delete", "Delete statement", "DELETE FROM ${1:table}\nWHERE ${2:condition};"),
        ("create", "Create table", "CREATE TABLE ${1:table_name} (\n\t${2:column_name} ${3:data_type} ${4:constraints},\n\t${0}\n);"),
        ("join", "Inner join", "SELECT ${1:columns}\nFROM ${2:table1}\nINNER JOIN ${3:table2} ON ${4:condition};"),
        ("leftjoin", "Left join", "SELECT ${1:columns}\nFROM ${2:table1}\nLEFT JOIN ${3:table2} ON ${4:condition};"),
        ("groupby", "Group by", "SELECT ${1:columns}\nFROM ${2:table}\nGROUP BY ${3:column}\nHAVING ${4:condition};"),
        ("orderby", "Order by", "SELECT ${1:columns}\nFROM ${2:table}\nORDER BY ${3:column} ${4:ASC};"),
    ]),
    
    ("dockerfile", &[
        ("from", "From image", "FROM ${1:ubuntu:latest}"),
        ("run", "Run command", "RUN ${1:command}"),
        ("copy", "Copy files", "COPY ${1:source} ${2:destination}"),
        ("add", "Add files", "ADD ${1:source} ${2:destination}"),
        ("workdir", "Working directory", "WORKDIR ${1:/app}"),
        ("expose", "Expose port", "EXPOSE ${1:8080}"),
        ("env", "Environment variable", "ENV ${1:KEY}=${2:value}"),
        ("cmd", "Default command", "CMD [\"${1:command}\", \"${2:arg1}\"]"),
        ("entrypoint", "Entry point", "ENTRYPOINT [\"${1:command}\"]"),
        ("volume", "Volume", "VOLUME ${1:/data}"),
        ("user", "User", "USER ${1:username}"),
        ("arg", "Build argument", "ARG ${1:NAME}=${2:default_value}"),
        ("label", "Label", "LABEL ${1:key}=\"${2:value}\""),
        ("healthcheck", "Health check", "HEALTHCHECK --interval=${1:30s} --timeout=${2:3s} --retries=${3:3} CMD ${4:command}"),
    ]),
    
    ("yaml", &[
        ("map", "YAML mapping", "${1:key}: ${2:value}"),
        ("list", "YAML list", "- ${1:item1}\n- ${2:item2}"),
        ("string", "YAML string", "${1:key}: \"${2:value}\""),
        ("multiline", "YAML multiline", "${1:key}: |\n  ${2:line1}\n  ${3:line2}"),
        ("number", "YAML number", "${1:key}: ${2:42}"),
        ("boolean", "YAML boolean", "${1:key}: ${2:true}"),
        ("null", "YAML null", "${1:key}: null"),
        ("anchor", "YAML anchor", "${1:key}: &${2:anchor} ${3:value}"),
        ("reference", "YAML reference", "${1:key}: *${2:anchor}"),
    ]),
    
    ("json", &[
        ("object", "JSON object", "{\n\t\"${1:key}\": \"${2:value}\"\n}"),
        ("array", "JSON array", "[\n\t\"${1:item1}\",\n\t\"${2:item2}\"\n]"),
        ("string", "JSON string", "\"${1:key}\": \"${2:value}\""),
        ("number", "JSON number", "\"${1:key}\": ${2:42}"),
        ("boolean", "JSON boolean", "\"${1:key}\": ${2:true}"),
        ("null", "JSON null", "\"${1:key}\": null"),
    ]),
];

// Template variables for dynamic content
struct TemplateVariable {
    name: String,
    value_type: VariableType,
    processor: fn(&TemplateContext) -> String,
}

enum VariableType {
    Static(String),
    Dynamic(fn(&TemplateContext) -> String),
    UserDefined(String),
}

const TEMPLATE_VARIABLES: &[(&str, VariableType)] = &[
    // Timestamp variables
    ("DAY", VariableType::Dynamic(|_| chrono::Local::now().format("%a").to_string())),
    ("DAY_FULL", VariableType::Dynamic(|_| chrono::Local::now().format("%A").to_string())),
    ("DATE", VariableType::Dynamic(|_| chrono::Local::now().format("%d").to_string())),
    ("MONTH", VariableType::Dynamic(|_| chrono::Local::now().format("%m").to_string())),
    ("MONTH_SHORT", VariableType::Dynamic(|_| chrono::Local::now().format("%b").to_string())),
    ("MONTH_FULL", VariableType::Dynamic(|_| chrono::Local::now().format("%B").to_string())),
    ("YEAR", VariableType::Dynamic(|_| chrono::Local::now().format("%Y").to_string())),
    ("TODAY", VariableType::Dynamic(|_| chrono::Local::now().format("%a, %b %d, %Y, %H:%M %Z").to_string())),
    ("TIME", VariableType::Dynamic(|_| chrono::Local::now().format("%T %Z").to_string())),
    ("TIME_12", VariableType::Dynamic(|_| chrono::Local::now().format("%r").to_string())),
    ("TIMESTAMP", VariableType::Dynamic(|_| chrono::Local::now().format("%A %b %d, %Y %T %Z").to_string())),
    ("SETVER", VariableType::Dynamic(|_| chrono::Local::now().format("%m%d%Y%H%M").to_string())),
    ("VERSION_TIMESTAMP", VariableType::Dynamic(|_| chrono::Local::now().format("%Y%m%d%H%M").to_string())),
    ("DATE_MM_DD_YYYY", VariableType::Dynamic(|_| chrono::Local::now().format("%m/%d/%Y").to_string())),
    ("TIME_HH_MM", VariableType::Dynamic(|_| chrono::Local::now().format("%H:%M").to_string())),
    ("FULL_TIMESTAMP", VariableType::Dynamic(|_| chrono::Local::now().format("%A, %b %d, %Y %H:%M %Z").to_string())),
    
    // User/Author variables
    ("NAME", VariableType::UserDefined("author_name".to_string())),
    ("EMAIL", VariableType::UserDefined("author_email".to_string())),
    ("HOSTNAME", VariableType::Dynamic(|_| gethostname::gethostname().to_string_lossy().to_string())),
    
    // File variables
    ("FILE", VariableType::Dynamic(|ctx| ctx.file_stem.clone())),
    ("FILEE", VariableType::Dynamic(|ctx| ctx.file_name.clone())),
    ("FILEF", VariableType::Dynamic(|ctx| ctx.file_path.clone())),
    ("FILER", VariableType::Dynamic(|ctx| ctx.relative_path.clone())),
    
    // Project variables
    ("PROJECT", VariableType::UserDefined("project_name".to_string())),
    ("COMPANY", VariableType::UserDefined("company_name".to_string())),
    
    // License variables
    ("LICENSE", VariableType::UserDefined("license".to_string())),
    ("COPYRIGHT", VariableType::UserDefined("copyright".to_string())),
    
    // Language-specific variables
    ("MACRO_GUARD", VariableType::Dynamic(|ctx| ctx.file_name.to_uppercase().replace('.', "_").replace('-', "_"))),
    ("MACRO_GUARD_FULL", VariableType::Dynamic(|ctx| ctx.relative_path.to_uppercase().replace('/', "_").replace('.', "_").replace('-', "_"))),
    ("CLASS", VariableType::Dynamic(|ctx| ctx.file_stem.clone())),
    ("CAMEL_CLASS", VariableType::Dynamic(|ctx| {
        ctx.file_stem.split('_').map(|word| {
            let mut chars: Vec<char> = word.chars().collect();
            if !chars.is_empty() {
                chars[0] = chars[0].to_uppercase().next().unwrap_or(chars[0]);
            }
            chars.into_iter().collect::<String>()
        }).collect()
    })),
];

// Comprehensive file header template
const COMPREHENSIVE_HEADER_TEMPLATE: &str = r#"# - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
##@Version           :  {{VERSION_TIMESTAMP}}-git
# @@Author           :  {{NAME}}
# @@Contact          :  {{EMAIL}}
# @@License          :  {{LICENSE}}
# @@ReadME           :  {{FILE}} --help
# @@Copyright        :  {{COPYRIGHT}} {{COMPANY}}
# @@Created          :  {{TIMESTAMP}}
# @@File             :  {{FILE}}
# @@Description      :  ${1:Description}
# @@Changelog        :  ${2:}
# @@TODO             :  ${3:}
# @@Other            :  ${4:}
# @@Resource         :  ${5:}
# @@Terminal App     :  ${6|yes,no|}
# @@sudo/root        :  ${7|yes,no|}
# @@Template         :  ${8:}
# - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

${0}"#;

// Blog post template
const BLOG_POST_TEMPLATE: &str = r#"---
title: ${1:Title}
date: "{{DATE_YYYY_MM_DD}}"
author: "{{NAME}}"
tags: [blog,${2:tags}]
categories: [personal,${3:category}]
comments: true
---

${4:Content}

"#;
```

### 3.3 Template Engine
```rust
struct TemplateEngine {
    file_templates: HashMap<String, FileTemplate>,
    template_processor: TemplateProcessor,
    template_paths: Vec<PathBuf>,
    auto_initialize: bool,
}

struct FileTemplate {
    name: String,
    file_patterns: Vec<String>,
    content: String,
    variables: Vec<String>,
    language: Option<String>,
}

// Built-in file templates by extension
const FILE_TEMPLATES: &[(&str, &str)] = &[
    ("rs", r#"//! {{FILE}} - {{PROJECT}}
//! 
//! {{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
//! Created: {{TIMESTAMP}}
//! Author: {{NAME}} <{{EMAIL}}>

{{CURSOR}}
"#),

    ("py", r#"#!/usr/bin/env python3
"""{{FILE}} - {{PROJECT}}

{{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
Created: {{TIMESTAMP}}
Author: {{NAME}} <{{EMAIL}}>
License: {{LICENSE}}
"""

{{CURSOR}}
"#),

    ("js", r#"/**
 * {{FILE}} - {{PROJECT}}
 * 
 * {{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
 * Created: {{TIMESTAMP}}
 * Author: {{NAME}} <{{EMAIL}}>
 * License: {{LICENSE}}
 */

{{CURSOR}}
"#),

    ("ts", r#"/**
 * {{FILE}} - {{PROJECT}}
 * 
 * {{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
 * Created: {{TIMESTAMP}}
 * Author: {{NAME}} <{{EMAIL}}>
 * License: {{LICENSE}}
 */

{{CURSOR}}
"#),

    ("html", r#"<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta name="author" content="{{NAME}}">
    <title>{{FILE}}</title>
    <!-- 
    {{FILE}} - {{PROJECT}}
    {{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
    Author: {{NAME}} <{{EMAIL}}>
    -->
</head>
<body>
    {{CURSOR}}
</body>
</html>
"#),

    ("css", r#"/**
 * {{FILE}} - {{PROJECT}}
 * 
 * {{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
 * Created: {{TIMESTAMP}}
 * Author: {{NAME}} <{{EMAIL}}>
 * License: {{LICENSE}}
 */

{{CURSOR}}
"#),

    ("scss", r#"/**
 * {{FILE}} - {{PROJECT}}
 * 
 * {{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
 * Created: {{TIMESTAMP}}
 * Author: {{NAME}} <{{EMAIL}}>
 * License: {{LICENSE}}
 */

{{CURSOR}}
"#),

    ("go", r#"// {{FILE}} - {{PROJECT}}
// 
// {{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
// Created: {{TIMESTAMP}}
// Author: {{NAME}} <{{EMAIL}}>
// License: {{LICENSE}}

package main

{{CURSOR}}
"#),

    ("java", r#"/**
 * {{FILE}} - {{PROJECT}}
 * 
 * {{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
 * Created: {{TIMESTAMP}}
 * Author: {{NAME}} <{{EMAIL}}>
 * License: {{LICENSE}}
 */

public class {{CLASS}} {
    {{CURSOR}}
}
"#),

    ("php", r#"<?php
/**
 * {{FILE}} - {{PROJECT}}
 * 
 * {{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
 * Created: {{TIMESTAMP}}
 * Author: {{NAME}} <{{EMAIL}}>
 * License: {{LICENSE}}
 */

{{CURSOR}}
"#),

    ("c", r#"/**
 * {{FILE}} - {{PROJECT}}
 * 
 * {{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
 * Created: {{TIMESTAMP}}
 * Author: {{NAME}} <{{EMAIL}}>
 * License: {{LICENSE}}
 */

#include <stdio.h>

{{CURSOR}}
"#),

    ("cpp", r#"/**
 * {{FILE}} - {{PROJECT}}
 * 
 * {{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
 * Created: {{TIMESTAMP}}
 * Author: {{NAME}} <{{EMAIL}}>
 * License: {{LICENSE}}
 */

#include <iostream>

{{CURSOR}}
"#),

    ("h", r#"/**
 * {{FILE}} - {{PROJECT}}
 * 
 * {{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
 * Created: {{TIMESTAMP}}
 * Author: {{NAME}} <{{EMAIL}}>
 * License: {{LICENSE}}
 */

#ifndef {{MACRO_GUARD}}_H
#define {{MACRO_GUARD}}_H

{{CURSOR}}

#endif /* {{MACRO_GUARD}}_H */
"#),

    ("hpp", r#"/**
 * {{FILE}} - {{PROJECT}}
 * 
 * {{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
 * Created: {{TIMESTAMP}}
 * Author: {{NAME}} <{{EMAIL}}>
 * License: {{LICENSE}}
 */

#ifndef {{MACRO_GUARD}}_HPP
#define {{MACRO_GUARD}}_HPP

{{CURSOR}}

#endif /* {{MACRO_GUARD}}_HPP */
"#),

    ("sh", r#"#!/bin/bash
# {{FILE}} - {{PROJECT}}
# 
# {{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
# Created: {{TIMESTAMP}}
# Author: {{NAME}} <{{EMAIL}}>
# License: {{LICENSE}}

set -euo pipefail

{{CURSOR}}
"#),

    ("bash", r#"#!/bin/bash
# {{FILE}} - {{PROJECT}}
# 
# {{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
# Created: {{TIMESTAMP}}
# Author: {{NAME}} <{{EMAIL}}>
# License: {{LICENSE}}

set -euo pipefail

{{CURSOR}}
"#),

    ("zsh", r#"#!/bin/zsh
# {{FILE}} - {{PROJECT}}
# 
# {{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
# Created: {{TIMESTAMP}}
# Author: {{NAME}} <{{EMAIL}}>
# License: {{LICENSE}}

{{CURSOR}}
"#),

    ("md", r#"# {{CLASS}}

{{CURSOR}}

---

*{{PROJECT}}*  
*{{COPYRIGHT}} {{YEAR}}, {{COMPANY}}*  
*Author: {{NAME}} <{{EMAIL}}>*
"#),

    ("yaml", r#"# {{FILE}} - {{PROJECT}}
# {{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
# Created: {{TIMESTAMP}}
# Author: {{NAME}} <{{EMAIL}}>

{{CURSOR}}
"#),

    ("yml", r#"# {{FILE}} - {{PROJECT}}
# {{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
# Created: {{TIMESTAMP}}
# Author: {{NAME}} <{{EMAIL}}>

{{CURSOR}}
"#),

    ("json", r#"{
  "_comment": "{{FILE}} - {{PROJECT}}",
  "_author": "{{NAME}} <{{EMAIL}}>",
  "_created": "{{TIMESTAMP}}",
  {{CURSOR}}
}
"#),

    ("xml", r#"<?xml version="1.0" encoding="UTF-8"?>
<!--
{{FILE}} - {{PROJECT}}
{{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
Created: {{TIMESTAMP}}
Author: {{NAME}} <{{EMAIL}}>
-->

{{CURSOR}}
"#),

    ("sql", r#"-- {{FILE}} - {{PROJECT}}
-- {{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
-- Created: {{TIMESTAMP}}
-- Author: {{NAME}} <{{EMAIL}}>

{{CURSOR}}
"#),

    ("dockerfile", r#"# {{FILE}} - {{PROJECT}}
# {{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
# Created: {{TIMESTAMP}}
# Author: {{NAME}} <{{EMAIL}}>

FROM alpine:latest

{{CURSOR}}
"#),

    ("makefile", r#"# {{FILE}} - {{PROJECT}}
# {{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
# Created: {{TIMESTAMP}}
# Author: {{NAME}} <{{EMAIL}}>

.PHONY: all clean

all:
	{{CURSOR}}

clean:
	rm -f *.o

"#),

    ("gitignore", r#"# {{FILE}} - {{PROJECT}}
# {{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
# Created: {{TIMESTAMP}}

# Dependencies
node_modules/
target/
build/
dist/

# Logs
*.log
logs/

# Environment
.env
.env.local

# IDE
.vscode/
.idea/
*.swp
*.swo

# OS
.DS_Store
Thumbs.db

{{CURSOR}}
"#),

    ("toml", r#"# {{FILE}} - {{PROJECT}}
# {{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
# Created: {{TIMESTAMP}}
# Author: {{NAME}} <{{EMAIL}}>

{{CURSOR}}
"#),

    ("ini", r#"# {{FILE}} - {{PROJECT}}
# {{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
# Created: {{TIMESTAMP}}
# Author: {{NAME}} <{{EMAIL}}>

{{CURSOR}}
"#),

    ("conf", r#"# {{FILE}} - {{PROJECT}}
# {{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
# Created: {{TIMESTAMP}}
# Author: {{NAME}} <{{EMAIL}}>

{{CURSOR}}
"#),

    ("rb", r#"#!/usr/bin/env ruby
# {{FILE}} - {{PROJECT}}
# 
# {{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
# Created: {{TIMESTAMP}}
# Author: {{NAME}} <{{EMAIL}}>
# License: {{LICENSE}}

{{CURSOR}}
"#),

    ("pl", r#"#!/usr/bin/env perl
# {{FILE}} - {{PROJECT}}
# 
# {{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
# Created: {{TIMESTAMP}}
# Author: {{NAME}} <{{EMAIL}}>
# License: {{LICENSE}}

use strict;
use warnings;

{{CURSOR}}
"#),

    ("lua", r#"#!/usr/bin/env lua
-- {{FILE}} - {{PROJECT}}
-- 
-- {{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
-- Created: {{TIMESTAMP}}
-- Author: {{NAME}} <{{EMAIL}}>
-- License: {{LICENSE}}

{{CURSOR}}
"#),

    ("vim", r#"\" {{FILE}} - {{PROJECT}}
\" {{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
\" Created: {{TIMESTAMP}}
\" Author: {{NAME}} <{{EMAIL}}>

{{CURSOR}}
"#),

    ("tex", r#"% {{FILE}} - {{PROJECT}}
% {{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
% Created: {{TIMESTAMP}}
% Author: {{NAME}} <{{EMAIL}}>

\documentclass{article}

\begin{document}

{{CURSOR}}

\end{document}
"#),
];
```

---

## 4. Container Management

### 4.1 Built-in Container Runtime
```rust
struct BuiltinContainerRuntime {
    runtime_engine: ContainerEngine,
    image_manager: ImageManager,
    network_manager: NetworkManager,
    storage_manager: StorageManager,
    resource_manager: ResourceManager,
}

struct ContainerEngine {
    runtime_type: RuntimeType::Builtin,
    oci_compliance: true,
    runtime_path: PathBuf,
    container_root: PathBuf,
    state_dir: PathBuf,
}

enum RuntimeType {
    Builtin,    // Our embedded runtime (youki/runc)
}

struct ImageManager {
    image_store: PathBuf,
    registry_cache: HashMap<String, RegistryInfo>,
    layer_cache: HashMap<String, LayerInfo>,
    manifest_cache: HashMap<String, ManifestInfo>,
    pull_strategies: PullStrategies,
}

struct PullStrategies {
    always: false,          // Only pull if not present locally
    if_not_present: true,   // Default strategy
    never: false,           // Use local only
}

struct RegistryInfo {
    url: String,
    auth: Option<RegistryAuth>,
    tls_verify: bool,
    timeout: Duration,
}

struct RegistryAuth {
    username: String,
    password: String,
    token: Option<String>,
}

// Default registry configuration
const DEFAULT_REGISTRIES: &[(&str, &str)] = &[
    ("docker.io", "https://index.docker.io/v1/"),
    ("ghcr.io", "https://ghcr.io"),
    ("quay.io", "https://quay.io"),
    ("gcr.io", "https://gcr.io"),
    ("registry.gitlab.com", "https://registry.gitlab.com"),
];

struct NetworkManager {
    bridge_name: String,
    subnet: ipnet::Ipv4Net,
    port_allocator: PortAllocator,
    dns_config: DnsConfig,
    firewall_rules: FirewallRules,
}

struct PortAllocator {
    port_range: std::ops::Range<u16>,
    allocated_ports: HashSet<u16>,
    reservations: HashMap<String, u16>, // container_id -> port
}

struct DnsConfig {
    nameservers: Vec<std::net::IpAddr>,
    search_domains: Vec<String>,
    options: Vec<String>,
}

struct FirewallRules {
    allow_outbound: bool,
    allow_inbound_ports: Vec<u16>,
    blocked_networks: Vec<ipnet::IpNet>,
    rate_limiting: Option<RateLimit>,
}

struct StorageManager {
    storage_driver: StorageDriver::Overlay2,
    storage_root: PathBuf,
    max_size: Option<u64>,
    cleanup_policy: CleanupPolicy,
}

enum StorageDriver {
    Overlay2,   // Default and most efficient
    Vfs,        // Fallback for systems without overlay support
}

struct CleanupPolicy {
    max_age: Duration,              // Default: 7 days
    max_total_size: Option<u64>,    // Default: 10GB
    keep_recent: usize,             // Default: 5 images
    aggressive_cleanup: bool,        // Default: false
}

struct ResourceManager {
    cgroup_version: CgroupVersion,
    default_limits: ResourceLimits,
    enforcement: ResourceEnforcement,
}

enum CgroupVersion {
    V1,
    V2,     // Preferred if available
}

struct ResourceLimits {
    cpu_limit: CpuLimit,
    memory_limit: MemoryLimit,
    disk_limit: DiskLimit,
    network_limit: NetworkLimit,
    process_limit: ProcessLimit,
}

struct CpuLimit {
    max_cores: f64,                 // Default: 2.0
    cpu_shares: Option<u64>,        // Default: 1024
    cpu_quota: Option<u64>,         // Default: None
    cpu_period: Option<u64>,        // Default: None
}

struct MemoryLimit {
    max_memory: u64,                // Default: 4GB
    swap_limit: Option<u64>,        // Default: None (no swap)
    oom_kill_disable: bool,         // Default: false
    memory_swappiness: Option<u64>, // Default: None
}

struct DiskLimit {
    max_size: u64,                  // Default: 20GB
    max_inodes: Option<u64>,        // Default: None
    device_read_bps: Option<u64>,   // Default: None
    device_write_bps: Option<u64>,  // Default: None
}

struct NetworkLimit {
    max_bandwidth: Option<u64>,     // Default: None
    max_connections: Option<u64>,   // Default: None
    allowed_ports: Vec<u16>,        // Default: 3000-9000
}

struct ProcessLimit {
    max_processes: u64,             // Default: 1024
    max_files: u64,                 // Default: 65536
}

struct ResourceEnforcement {
    strict_limits: true,            // Enforce all limits strictly
    oom_score_adj: i32,            // Default: 500
    nice_level: i32,               // Default: 0
    ionice_class: Option<u32>,     // Default: None
    ionice_priority: Option<u32>,  // Default: None
}
```

### 4.2 Container Lifecycle Management
```rust
struct ContainerLifecycleManager {
    containers: HashMap<String, ManagedContainer>,
    lifecycle_hooks: LifecycleHooks,
    health_checker: HealthChecker,
    auto_cleanup: AutoCleanup,
}

struct ManagedContainer {
    id: String,
    name: String,
    image: String,
    status: ContainerStatus,
    config: ContainerConfig,
    runtime_info: RuntimeInfo,
    resource_usage: ResourceUsage,
    health_status: HealthStatus,
    created_at: chrono::DateTime<chrono::Utc>,
    last_activity: chrono::DateTime<chrono::Utc>,
}

enum ContainerStatus {
    Creating,
    Running,
    Paused,
    Stopped,
    Sleeping,
    Hibernating,
    Failed,
    Terminated,
}

struct RuntimeInfo {
    pid: Option<u32>,
    exit_code: Option<i32>,
    started_at: Option<chrono::DateTime<chrono::Utc>>,
    finished_at: Option<chrono::DateTime<chrono::Utc>>,
    restart_count: u32,
}

struct ResourceUsage {
    cpu_usage: CpuUsage,
    memory_usage: MemoryUsage,
    disk_usage: DiskUsage,
    network_usage: NetworkUsage,
    last_updated: chrono::DateTime<chrono::Utc>,
}

struct CpuUsage {
    usage_percent: f64,
    total_usage: Duration,
    throttled_time: Duration,
}

struct MemoryUsage {
    usage_bytes: u64,
    max_usage: u64,
    cache_bytes: u64,
    swap_usage: u64,
}

struct DiskUsage {
    read_bytes: u64,
    write_bytes: u64,
    read_ops: u64,
    write_ops: u64,
}

struct NetworkUsage {
    rx_bytes: u64,
    tx_bytes: u64,
    rx_packets: u64,
    tx_packets: u64,
}

struct HealthStatus {
    status: Health,
    last_check: chrono::DateTime<chrono::Utc>,
    failure_count: u32,
    check_command: Option<String>,
}

enum Health {
    Healthy,
    Unhealthy,
    Starting,
    Unknown,
}

struct LifecycleHooks {
    pre_create: Vec<Hook>,
    post_create: Vec<Hook>,
    pre_start: Vec<Hook>,
    post_start: Vec<Hook>,
    pre_stop: Vec<Hook>,
    post_stop: Vec<Hook>,
    pre_remove: Vec<Hook>,
    post_remove: Vec<Hook>,
}

struct Hook {
    name: String,
    command: Vec<String>,
    timeout: Duration,
    fail_on_error: bool,
}

struct HealthChecker {
    check_interval: Duration,       // Default: 30 seconds
    timeout: Duration,              // Default: 10 seconds
    retries: u32,                   // Default: 3
    start_period: Duration,         // Default: 60 seconds
}

struct AutoCleanup {
    enabled: bool,                  // Default: true
    cleanup_interval: Duration,     // Default: 1 hour
    max_stopped_age: Duration,      // Default: 24 hours
    max_failed_age: Duration,       // Default: 1 hour
    keep_last_n: usize,            // Default: 3
}
```

### 4.3 Project Configuration Support
```rust
struct ProjectConfigManager {
    config_resolvers: Vec<Box<dyn ConfigResolver>>,
    project_detector: ProjectDetector,
    config_cache: HashMap<String, ProjectConfig>,
}

trait ConfigResolver {
    fn name(&self) -> &str;
    fn priority(&self) -> u8;
    fn can_resolve(&self, path: &Path) -> bool;
    fn resolve(&self, path: &Path) -> Result<ProjectConfig>;
}

// DevContainer resolver (highest priority)
struct DevContainerResolver;

impl ConfigResolver for DevContainerResolver {
    fn name(&self) -> &str { "devcontainer" }
    fn priority(&self) -> u8 { 255 }
    
    fn can_resolve(&self, path: &Path) -> bool {
        path.join(".devcontainer/devcontainer.json").exists() ||
        path.join(".devcontainer.json").exists()
    }
    
    fn resolve(&self, path: &Path) -> Result<ProjectConfig> {
        let config_path = if path.join(".devcontainer/devcontainer.json").exists() {
            path.join(".devcontainer/devcontainer.json")
        } else {
            path.join(".devcontainer.json")
        };
        
        let content = std::fs::read_to_string(&config_path)?;
        let devcontainer: DevContainerSpec = serde_json::from_str(&content)?;
        
        Ok(ProjectConfig::from_devcontainer(devcontainer))
    }
}

#[derive(Deserialize)]
struct DevContainerSpec {
    name: Option<String>,
    image: Option<String>,
    dockerfile: Option<String>,
    docker_compose_file: Option<String>,
    service: Option<String>,
    
    // Build configuration
    build: Option<BuildConfig>,
    
    // Runtime configuration
    settings: Option<serde_json::Value>,
    extensions: Option<Vec<String>>,
    features: Option<HashMap<String, serde_json::Value>>,
    
    // Container configuration
    forwarded_ports: Option<Vec<u16>>,
    port_attributes: Option<HashMap<String, PortAttribute>>,
    mounts: Option<Vec<String>>,
    
    // Lifecycle
    on_create_command: Option<String>,
    update_content_command: Option<String>,
    post_create_command: Option<String>,
    post_start_command: Option<String>,
    post_attach_command: Option<String>,
    
    // User configuration
    remote_user: Option<String>,
    container_user: Option<String>,
    user_env_probe: Option<String>,
    
    // Environment
    remote_env: Option<HashMap<String, String>>,
    container_env: Option<HashMap<String, String>>,
    
    // Advanced
    privileged: Option<bool>,
    cap_add: Option<Vec<String>>,
    security_opt: Option<Vec<String>>,
    init: Option<bool>,
    
    // Workspace
    workspace_folder: Option<String>,
    workspace_mount: Option<String>,
    
    // Other
    shutdown_action: Option<String>,
    overrides_command: Option<bool>,
    run_args: Option<Vec<String>>,
}

#[derive(Deserialize)]
struct BuildConfig {
    dockerfile: Option<String>,
    context: Option<String>,
    args: Option<HashMap<String, String>>,
    target: Option<String>,
    cache_from: Option<Vec<String>>,
}

#[derive(Deserialize)]
struct PortAttribute {
    label: Option<String>,
    on_auto_forward: Option<String>,
    elevate_if_needed: Option<bool>,
    require_local_port: Option<bool>,
}

// Gitpod resolver (medium priority)
struct GitpodResolver;

impl ConfigResolver for GitpodResolver {
    fn name(&self) -> &str { "gitpod" }
    fn priority(&self) -> u8 { 128 }
    
    fn can_resolve(&self, path: &Path) -> bool {
        path.join(".gitpod.yml").exists() ||
        path.join(".gitpod.yaml").exists()
    }
    
    fn resolve(&self, path: &Path) -> Result<ProjectConfig> {
        let config_path = if path.join(".gitpod.yml").exists() {
            path.join(".gitpod.yml")
        } else {
            path.join(".gitpod.yaml")
        };
        
        let content = std::fs::read_to_string(&config_path)?;
        let gitpod: GitpodSpec = serde_yaml::from_str(&content)?;
        
        Ok(ProjectConfig::from_gitpod(gitpod))
    }
}

#[derive(Deserialize)]
struct GitpodSpec {
    image: Option<String>,
    
    // Tasks
    tasks: Option<Vec<GitpodTask>>,
    
    // Ports
    ports: Option<Vec<GitpodPort>>,
    
    // GitHub integration
    github: Option<GitpodGitHub>,
    
    // IDE configuration
    vscode: Option<GitpodVSCode>,
    jetbrains: Option<GitpodJetBrains>,
    
    // Workspace configuration
    workspace: Option<GitpodWorkspace>,
    
    // Git configuration
    git_config: Option<HashMap<String, String>>,
    
    // Experimental features
    experimental: Option<GitpodExperimental>,
}

#[derive(Deserialize)]
struct GitpodTask {
    name: Option<String>,
    init: Option<String>,
    before: Option<String>,
    command: Option<String>,
    env: Option<HashMap<String, String>>,
    open_in: Option<String>,
    open_mode: Option<String>,
}

#[derive(Deserialize)]
struct GitpodPort {
    port: u16,
    on_open: Option<String>,
    visibility: Option<String>,
    name: Option<String>,
    description: Option<String>,
    protocol: Option<String>,
}

#[derive(Deserialize)]
struct GitpodGitHub {
    prebuilds: Option<GitpodPrebuilds>,
}

#[derive(Deserialize)]
struct GitpodPrebuilds {
    master: Option<bool>,
    branches: Option<bool>,
    pull_requests: Option<bool>,
    pull_requests_from_forks: Option<bool>,
    add_comment: Option<bool>,
    add_badge: Option<bool>,
    add_label: Option<bool>,
}

#[derive(Deserialize)]
struct GitpodVSCode {
    extensions: Option<Vec<String>>,
}

#[derive(Deserialize)]
struct GitpodJetBrains {
    intellij: Option<GitpodJetBrainsIDE>,
    goland: Option<GitpodJetBrainsIDE>,
    pycharm: Option<GitpodJetBrainsIDE>,
    phpstorm: Option<GitpodJetBrainsIDE>,
    webstorm: Option<GitpodJetBrainsIDE>,
    rider: Option<GitpodJetBrainsIDE>,
    clion: Option<GitpodJetBrainsIDE>,
    rubymine: Option<GitpodJetBrainsIDE>,
}

#[derive(Deserialize)]
struct GitpodJetBrainsIDE {
    plugins: Option<Vec<String>>,
    preferences: Option<HashMap<String, serde_json::Value>>,
}

#[derive(Deserialize)]
struct GitpodWorkspace {
    checkout_location: Option<String>,
}

#[derive(Deserialize)]
struct GitpodExperimental {
    network_policies: Option<Vec<NetworkPolicy>>,
}

#[derive(Deserialize)]
struct NetworkPolicy {
    name: String,
    egress: Option<Vec<EgressRule>>,
}

#[derive(Deserialize)]
struct EgressRule {
    to: Option<Vec<String>>,
    ports: Option<Vec<u16>>,
}

// Editor config resolver (lower priority)
struct EditorConfigResolver;

impl ConfigResolver for EditorConfigResolver {
    fn name(&self) -> &str { "editor_config" }
    fn priority(&self) -> u8 { 64 }
    
    fn can_resolve(&self, path: &Path) -> bool {
        path.join(".vscode/settings.json").exists() ||
        path.join(".editor/settings.json").exists()
    }
    
    fn resolve(&self, path: &Path) -> Result<ProjectConfig> {
        let config_path = if path.join(".vscode/settings.json").exists() {
            path.join(".vscode/settings.json")
        } else {
            path.join(".editor/settings.json")
        };
        
        let content = std::fs::read_to_string(&config_path)?;
        let settings: EditorSettings = serde_json::from_str(&content)?;
        
        Ok(ProjectConfig::from_editor_settings(settings))
    }
}

#[derive(Deserialize)]
struct EditorSettings {
    // Editor configuration
    #[serde(rename = "editor.fontSize")]
    editor_font_size: Option<u32>,
    
    #[serde(rename = "editor.fontFamily")]
    editor_font_family: Option<String>,
    
    #[serde(rename = "editor.tabSize")]
    editor_tab_size: Option<u32>,
    
    #[serde(rename = "editor.insertSpaces")]
    editor_insert_spaces: Option<bool>,
    
    #[serde(rename = "editor.wordWrap")]
    editor_word_wrap: Option<String>,
    
    #[serde(rename = "editor.formatOnSave")]
    editor_format_on_save: Option<bool>,
    
    // Language-specific settings
    #[serde(flatten)]
    language_settings: HashMap<String, serde_json::Value>,
    
    // Extensions and features
    extensions: Option<Vec<String>>,
    features: Option<HashMap<String, bool>>,
    
    // Terminal settings
    #[serde(rename = "terminal.integrated.shell.linux")]
    terminal_shell_linux: Option<String>,
    
    #[serde(rename = "terminal.integrated.shell.osx")]
    terminal_shell_osx: Option<String>,
    
    #[serde(rename = "terminal.integrated.shell.windows")]
    terminal_shell_windows: Option<String>,
    
    #[serde(rename = "terminal.integrated.fontSize")]
    terminal_font_size: Option<u32>,
    
    // Git settings
    #[serde(rename = "git.autofetch")]
    git_autofetch: Option<bool>,
    
    #[serde(rename = "git.enableSmartCommit")]
    git_enable_smart_commit: Option<bool>,
    
    // Other settings
    #[serde(flatten)]
    other: HashMap<String, serde_json::Value>,
}

// Auto-detection resolver (lowest priority)
struct AutoDetectionResolver;

impl ConfigResolver for AutoDetectionResolver {
    fn name(&self) -> &str { "auto_detection" }
    fn priority(&self) -> u8 { 1 }
    
    fn can_resolve(&self, _path: &Path) -> bool {
        true // Always can provide fallback
    }
    
    fn resolve(&self, path: &Path) -> Result<ProjectConfig> {
        let project_type = ProjectDetector::detect(path);
        Ok(ProjectConfig::from_project_type(project_type))
    }
}

struct ProjectDetector;

impl ProjectDetector {
    fn detect(path: &Path) -> ProjectType {
        // Language-specific files (order matters - more specific first)
        if path.join("Cargo.toml").exists() {
            return ProjectType::Rust;
        }
        
        if path.join("package.json").exists() {
            if path.join("tsconfig.json").exists() {
                return ProjectType::TypeScript;
            }
            return ProjectType::JavaScript;
        }
        
        if path.join("go.mod").exists() || path.join("go.sum").exists() {
            return ProjectType::Go;
        }
        
        if path.join("pom.xml").exists() || 
           path.join("build.gradle").exists() || 
           path.join("build.gradle.kts").exists() {
            return ProjectType::Java;
        }
        
        if path.join("requirements.txt").exists() ||
           path.join("pyproject.toml").exists() ||
           path.join("setup.py").exists() ||
           path.join("Pipfile").exists() {
            return ProjectType::Python;
        }
        
        if path.join("composer.json").exists() {
            return ProjectType::PHP;
        }
        
        if path.join("Gemfile").exists() {
            return ProjectType::Ruby;
        }
        
        if path.join("mix.exs").exists() {
            return ProjectType::Elixir;
        }
        
        if path.join("pubspec.yaml").exists() {
            return ProjectType::Dart;
        }
        
        if path.join("stack.yaml").exists() || 
           path.join("cabal.project").exists() {
            return ProjectType::Haskell;
        }
        
        if path.join("shard.yml").exists() {
            return ProjectType::Crystal;
        }
        
        if path.join("dub.json").exists() || 
           path.join("dub.sdl").exists() {
            return ProjectType::D;
        }
        
        if path.join("Project.toml").exists() {
            return ProjectType::Julia;
        }
        
        if path.join("elm.json").exists() {
            return ProjectType::Elm;
        }
        
        if path.join("nim.cfg").exists() ||
           self.has_nim_files(path) {
            return ProjectType::Nim;
        }
        
        if path.join("zig.json").exists() ||
           self.has_zig_files(path) {
            return ProjectType::Zig;
        }
        
        // Framework-specific detection
        if path.join("next.config.js").exists() ||
           path.join("next.config.ts").exists() {
            return ProjectType::NextJS;
        }
        
        if path.join("nuxt.config.js").exists() ||
           path.join("nuxt.config.ts").exists() {
            return ProjectType::NuxtJS;
        }
        
        if path.join("svelte.config.js").exists() {
            return ProjectType::Svelte;
        }
        
        if path.join("vue.config.js").exists() ||
           self.has_vue_files(path) {
            return ProjectType::Vue;
        }
        
        if path.join("angular.json").exists() {
            return ProjectType::Angular;
        }
        
        if path.join("ember-cli-build.js").exists() {
            return ProjectType::Ember;
        }
        
        // Build system detection
        if path.join("CMakeLists.txt").exists() {
            return ProjectType::CMake;
        }
        
        if path.join("Makefile").exists() ||
           path.join("makefile").exists() {
            return ProjectType::Make;
        }
        
        if path.join("meson.build").exists() {
            return ProjectType::Meson;
        }
        
        // Container/deployment detection
        if path.join("Dockerfile").exists() {
            return ProjectType::Docker;
        }
        
        if path.join("docker-compose.yml").exists() ||
           path.join("docker-compose.yaml").exists() {
            return ProjectType::DockerCompose;
        }
        
        if path.join("kubernetes").is_dir() ||
           path.join("k8s").is_dir() ||
           self.has_k8s_files(path) {
            return ProjectType::Kubernetes;
        }
        
        // Static site generators
        if path.join("_config.yml").exists() {
            return ProjectType::Jekyll;
        }
        
        if path.join("gatsby-config.js").exists() {
            return ProjectType::Gatsby;
        }
        
        if path.join("hugo.toml").exists() ||
           path.join("config.toml").exists() && path.join("content").is_dir() {
            return ProjectType::Hugo;
        }
        
        // Documentation
        if path.join("mkdocs.yml").exists() {
            return ProjectType::MkDocs;
        }
        
        if path.join("Sphinx").is_dir() ||
           path.join("conf.py").exists() && path.join("source").is_dir() {
            return ProjectType::Sphinx;
        }
        
        // Mobile development
        if path.join("android").is_dir() && path.join("ios").is_dir() {
            if path.join("lib").is_dir() && path.join("pubspec.yaml").exists() {
                return ProjectType::Flutter;
            }
            return ProjectType::ReactNative;
        }
        
        if path.join("app.json").exists() && path.join("App.js").exists() {
            return ProjectType::ReactNative;
        }
        
        // Web-specific
        if self.has_html_files(path) {
            if self.has_css_files(path) || self.has_js_files(path) {
                return ProjectType::WebApp;
            }
            return ProjectType::StaticSite;
        }
        
        // Fallback based on file extensions
        if self.has_c_files(path) {
            return ProjectType::C;
        }
        
        if self.has_cpp_files(path) {
            return ProjectType::Cpp;
        }
        
        if self.has_cs_files(path) {
            return ProjectType::CSharp;
        }
        
        if self.has_lua_files(path) {
            return ProjectType::Lua;
        }
        
        if self.has_perl_files(path) {
            return ProjectType::Perl;
        }
        
        if self.has_shell_files(path) {
            return ProjectType::Shell;
        }
        
        // Default
        ProjectType::Generic
    }
    
    fn has_nim_files(&self, path: &Path) -> bool {
        self.find_files_with_extension(path, "nim").len() > 0
    }
    
    fn has_zig_files(&self, path: &Path) -> bool {
        self.find_files_with_extension(path, "zig").len() > 0
    }
    
    fn has_vue_files(&self, path: &Path) -> bool {
        self.find_files_with_extension(path, "vue").len() > 0
    }
    
    fn has_k8s_files(&self, path: &Path) -> bool {
        let k8s_extensions = ["yaml", "yml"];
        for ext in &k8s_extensions {
            let files = self.find_files_with_extension(path, ext);
            for file in files {
                if let Ok(content) = std::fs::read_to_string(&file) {
                    if content.contains("apiVersion:") && content.contains("kind:") {
                        return true;
                    }
                }
            }
        }
        false
    }
    
    fn has_html_files(&self, path: &Path) -> bool {
        self.find_files_with_extension(path, "html").len() > 0
    }
    
    fn has_css_files(&self, path: &Path) -> bool {
        let css_extensions = ["css", "scss", "sass", "less"];
        css_extensions.iter().any(|ext| {
            self.find_files_with_extension(path, ext).len() > 0
        })
    }
    
    fn has_js_files(&self, path: &Path) -> bool {
        let js_extensions = ["js", "ts", "jsx", "tsx"];
        js_extensions.iter().any(|ext| {
            self.find_files_with_extension(path, ext).len() > 0
        })
    }
    
    fn has_c_files(&self, path: &Path) -> bool {
        let c_extensions = ["c", "h"];
        c_extensions.iter().any(|ext| {
            self.find_files_with_extension(path, ext).len() > 0
        })
    }
    
    fn has_cpp_files(&self, path: &Path) -> bool {
        let cpp_extensions = ["cpp", "cxx", "cc", "hpp", "hxx", "hh"];
        cpp_extensions.iter().any(|ext| {
            self.find_files_with_extension(path, ext).len() > 0
        })
    }
    
    fn has_cs_files(&self, path: &Path) -> bool {
        self.find_files_with_extension(path, "cs").len() > 0
    }
    
    fn has_lua_files(&self, path: &Path) -> bool {
        self.find_files_with_extension(path, "lua").len() > 0
    }
    
    fn has_perl_files(&self, path: &Path) -> bool {
        let perl_extensions = ["pl", "pm"];
        perl_extensions.iter().any(|ext| {
            self.find_files_with_extension(path, ext).len() > 0
        })
    }
    
    fn has_shell_files(&self, path: &Path) -> bool {
        let shell_extensions = ["sh", "bash", "zsh", "fish"];
        shell_extensions.iter().any(|ext| {
            self.find_files_with_extension(path, ext).len() > 0
        })
    }
    
    fn find_files_with_extension(&self, path: &Path, extension: &str) -> Vec<PathBuf> {
        let mut files = Vec::new();
        if let Ok(entries) = std::fs::read_dir(path) {
            for entry in entries.flatten() {
                let path = entry.path();
                if path.is_file() {
                    if let Some(ext) = path.extension() {
                        if ext == extension {
                            files.push(path);
                        }
                    }
                } else if path.is_dir() && !self.should_skip_directory(&path) {
                    files.extend(self.find_files_with_extension(&path, extension));
                }
            }
        }
        files
    }
    
    fn should_skip_directory(&self, path: &Path) -> bool {
        let skip_dirs = [
            "node_modules", ".git", "target", "build", "dist", 
            ".next", ".cache", "__pycache__", ".pytest_cache",
            "vendor", ".vendor", "deps", "_build"
        ];
        
        if let Some(dir_name) = path.file_name() {
            if let Some(dir_str) = dir_name.to_str() {
                return skip_dirs.contains(&dir_str) || dir_str.starts_with('.');
            }
        }
        false
    }
}

#[derive(Debug, Clone, PartialEq)]
enum ProjectType {
    // Languages
    Rust,
    JavaScript,
    TypeScript,
    Python,
    Go,
    Java,
    PHP,
    Ruby,
    C,
    Cpp,
    CSharp,
    Elixir,
    Dart,
    Haskell,
    Crystal,
    D,
    Julia,
    Elm,
    Nim,
    Zig,
    Lua,
    Perl,
    Shell,
    
    // Frameworks
    NextJS,
    NuxtJS,
    Svelte,
    Vue,
    Angular,
    Ember,
    ReactNative,
    Flutter,
    
    // Build Systems
    CMake,
    Make,
    Meson,
    
    // Containers & Deployment
    Docker,
    DockerCompose,
    Kubernetes,
    
    // Static Site Generators
    Jekyll,
    Gatsby,
    Hugo,
    
    // Documentation
    MkDocs,
    Sphinx,
    
    // Web
    WebApp,
    StaticSite,
    
    // Generic
    Generic,
}

// Unified project configuration
struct ProjectConfig {
    project_type: ProjectType,
    container_config: ContainerConfig,
    editor_config: EditorConfig,
    tools_config: ToolsConfig,
    environment_config: EnvironmentConfig,
    tasks_config: TasksConfig,
}

impl ProjectConfig {
    fn from_devcontainer(devcontainer: DevContainerSpec) -> Self {
        // Convert DevContainer spec to unified config
        // Implementation details...
        todo!()
    }
    
    fn from_gitpod(gitpod: GitpodSpec) -> Self {
        // Convert Gitpod spec to unified config
        // Implementation details...
        todo!()
    }
    
    fn from_editor_settings(settings: EditorSettings) -> Self {
        // Convert editor settings to unified config
        // Implementation details...
        todo!()
    }
    
    fn from_project_type(project_type: ProjectType) -> Self {
        // Generate default config based on project type
        let container_config = ContainerConfig::for_project_type(&project_type);
        let editor_config = EditorConfig::for_project_type(&project_type);
        let tools_config = ToolsConfig::for_project_type(&project_type);
        let environment_config = EnvironmentConfig::for_project_type(&project_type);
        let tasks_config = TasksConfig::for_project_type(&project_type);
        
        Self {
            project_type,
            container_config,
            editor_config,
            tools_config,
            environment_config,
            tasks_config,
        }
    }
}
```

---

## 5. Embedded Scheduler

### 5.1 Scheduler Architecture
```rust
struct EmbeddedScheduler {
    workspaces: HashMap<String, WorkspaceInstance>,
    resource_manager: ResourceManager,
    scheduler_engine: SchedulerEngine,
    lifecycle_manager: LifecycleManager,
    monitoring_system: MonitoringSystem,
    policies: SchedulingPolicies,
}

struct WorkspaceInstance {
    id: String,
    name: String,
    owner_id: String,
    project_path: PathBuf,
    container: ManagedContainer,
    status: WorkspaceStatus,
    resources: ResourceAllocation,
    activity: ActivityTracker,
    created_at: chrono::DateTime<chrono::Utc>,
    last_accessed: chrono::DateTime<chrono::Utc>,
    access_count: u64,
    priority: WorkspacePriority,
}

enum WorkspaceStatus {
    Initializing,   // Creating container, installing tools
    Ready,          // Available for use
    Active,         // User currently connected
    Idle,           // No user activity for some time
    Sleeping,       // Container paused to save resources
    Hibernating,    // Container stopped, state saved
    Terminating,    // Being cleaned up
    Failed,         // Error state
}

struct ActivityTracker {
    last_file_change: Option<chrono::DateTime<chrono::Utc>>,
    last_terminal_activity: Option<chrono::DateTime<chrono::Utc>>,
    last_web_interaction: Option<chrono::DateTime<chrono::Utc>>,
    total_active_time: Duration,
    file_changes_count: u64,
    terminal_commands_count: u64,
    web_requests_count: u64,
}

enum WorkspacePriority {
    Critical,   // Never automatically terminate
    High,       // Extended idle timeouts
    Normal,     // Default scheduling
    Low,        // First to be cleaned up
}

struct SchedulerEngine {
    scheduling_queue: VecDeque<SchedulingTask>,
    resource_balancer: ResourceBalancer,
    decision_engine: DecisionEngine,
    metrics_collector: MetricsCollector,
}

struct SchedulingTask {
    task_type: TaskType,
    workspace_id: String,
    scheduled_at: chrono::DateTime<chrono::Utc>,
    priority: TaskPriority,
    retry_count: u32,
    max_retries: u32,
}

enum TaskType {
    Create,
    Start,
    Stop,
    Pause,
    Resume,
    Hibernate,
    Terminate,
    ResourceUpdate,
    HealthCheck,
    Backup,
}

enum TaskPriority {
    Critical,   // Process immediately
    High,       // Process within 1 minute
    Normal,     // Process within 5 minutes
    Low,        // Process when resources available
}

struct ResourceBalancer {
    balancing_strategy: BalancingStrategy,
    resource_thresholds: ResourceThresholds,
    auto_scaling: AutoScalingConfig,
}

enum BalancingStrategy {
    RoundRobin,
    LeastLoaded,
    PriorityBased,
    ActivityBased,
}

struct ResourceThresholds {
    cpu_high_threshold: f64,        // 80%
    cpu_low_threshold: f64,         // 20%
    memory_high_threshold: f64,     // 85%
    memory_low_threshold: f64,      // 30%
    disk_high_threshold: f64,       // 90%
    disk_low_threshold: f64,        // 50%
    network_high_threshold: f64,    // 75%
}

struct AutoScalingConfig {
    enabled: bool,
    scale_up_cooldown: Duration,    // 5 minutes
    scale_down_cooldown: Duration,  // 15 minutes
    cpu_scale_up_threshold: f64,    // 70%
    cpu_scale_down_threshold: f64,  // 30%
    memory_scale_up_threshold: f64, // 80%
    memory_scale_down_threshold: f64, // 40%
}

struct DecisionEngine {
    decision_rules: Vec<DecisionRule>,
    machine_learning_model: Option<MLModel>,
    historical_data: HistoricalData,
}

struct DecisionRule {
    name: String,
    condition: Box<dyn Fn(&WorkspaceInstance, &SystemMetrics) -> bool>,
    action: SchedulingAction,
    weight: f64,
}

enum SchedulingAction {
    NoAction,
    Sleep,
    Hibernate,
    Terminate,
    IncreaseResources,
    DecreaseResources,
    ChangePriority(WorkspacePriority),
}

struct MLModel {
    model_type: ModelType,
    model_data: Vec<u8>,
    last_trained: chrono::DateTime<chrono::Utc>,
    accuracy: f64,
}

enum ModelType {
    LinearRegression,
    DecisionTree,
    NeuralNetwork,
    XGBoost,
}

struct HistoricalData {
    workspace_usage_patterns: HashMap<String, UsagePattern>,
    resource_utilization: Vec<ResourceDataPoint>,
    performance_metrics: Vec<PerformanceDataPoint>,
    retention_period: Duration, // 30 days
}

struct UsagePattern {
    user_id: String,
    typical_session_duration: Duration,
    preferred_work_hours: TimeRange,
    common_project_types: Vec<ProjectType>,
    resource_usage_profile: ResourceProfile,
}

struct TimeRange {
    start_hour: u8,
    end_hour: u8,
    timezone: chrono_tz::Tz,
}

struct ResourceProfile {
    average_cpu_usage: f64,
    peak_cpu_usage: f64,
    average_memory_usage: u64,
    peak_memory_usage: u64,
    network_intensity: NetworkIntensity,
}

enum NetworkIntensity {
    Low,    // Occasional git operations
    Medium, // Regular API calls, package downloads
    High,   // Streaming, large file transfers
}

struct ResourceDataPoint {
    timestamp: chrono::DateTime<chrono::Utc>,
    cpu_usage: f64,
    memory_usage: u64,
    disk_usage: u64,
    network_rx: u64,
    network_tx: u64,
    active_workspaces: u32,
}

struct PerformanceDataPoint {
    timestamp: chrono::DateTime<chrono::Utc>,
    response_time: Duration,
    throughput: f64,
    error_rate: f64,
    user_satisfaction: Option<f64>,
}

struct LifecycleManager {
    lifecycle_policies: LifecyclePolicies,
    state_machine: WorkspaceStateMachine,
    transition_handlers: HashMap<StateTransition, TransitionHandler>,
}

struct LifecyclePolicies {
    idle_timeout: Duration,         // 30 minutes
    sleep_timeout: Duration,        // 2 hours
    hibernation_timeout: Duration,  // 24 hours
    termination_timeout: Duration,  // 7 days
    
    priority_multipliers: HashMap<WorkspacePriority, f64>,
    project_type_multipliers: HashMap<ProjectType, f64>,
    
    backup_before_hibernation: bool,
    backup_before_termination: bool,
    
    grace_period: Duration,         // 5 minutes warning before action
    force_termination_timeout: Duration, // 30 seconds
}

struct WorkspaceStateMachine {
    states: HashMap<WorkspaceStatus, StateDefinition>,
    transitions: HashMap<StateTransition, TransitionDefinition>,
}

struct StateDefinition {
    name: String,
    description: String,
    entry_actions: Vec<Action>,
    exit_actions: Vec<Action>,
    allowed_transitions: Vec<WorkspaceStatus>,
    timeout: Option<Duration>,
    timeout_transition: Option<WorkspaceStatus>,
}

struct StateTransition {
    from: WorkspaceStatus,
    to: WorkspaceStatus,
}

struct TransitionDefinition {
    name: String,
    conditions: Vec<Condition>,
    actions: Vec<Action>,
    duration: Option<Duration>,
    rollback_actions: Vec<Action>,
}

struct TransitionHandler {
    handler: Box<dyn Fn(&mut WorkspaceInstance) -> Result<()>>,
    timeout: Duration,
    retry_policy: RetryPolicy,
}

struct Action {
    name: String,
    handler: Box<dyn Fn(&mut WorkspaceInstance) -> Result<()>>,
    timeout: Duration,
    required: bool,
}

struct Condition {
    name: String,
    checker: Box<dyn Fn(&WorkspaceInstance) -> bool>,
}

struct RetryPolicy {
    max_retries: u32,
    base_delay: Duration,
    max_delay: Duration,
    backoff_multiplier: f64,
}

struct MonitoringSystem {
    metrics_collector: MetricsCollector,
    health_checker: HealthChecker,
    alerting_system: AlertingSystem,
    performance_analyzer: PerformanceAnalyzer,
}

struct MetricsCollector {
    collection_interval: Duration,  // 30 seconds
    metrics_buffer: Vec<MetricDataPoint>,
    aggregation_window: Duration,   // 5 minutes
    retention_period: Duration,     // 30 days
}

struct MetricDataPoint {
    timestamp: chrono::DateTime<chrono::Utc>,
    workspace_id: String,
    metric_type: MetricType,
    value: f64,
    tags: HashMap<String, String>,
}

enum MetricType {
    CpuUsage,
    MemoryUsage,
    DiskUsage,
    NetworkRx,
    NetworkTx,
    ResponseTime,
    RequestCount,
    ErrorCount,
    UserSatisfaction,
    Custom(String),
}

struct HealthChecker {
    health_checks: Vec<HealthCheck>,
    check_interval: Duration,       // 1 minute
    failure_threshold: u32,         // 3 consecutive failures
    recovery_threshold: u32,        // 2 consecutive successes
}

struct HealthCheck {
    name: String,
    checker: Box<dyn Fn(&WorkspaceInstance) -> Result<HealthStatus>>,
    timeout: Duration,
    critical: bool,
}

struct AlertingSystem {
    alert_rules: Vec<AlertRule>,
    notification_channels: Vec<NotificationChannel>,
    alert_history: Vec<Alert>,
    suppression_rules: Vec<SuppressionRule>,
}

struct AlertRule {
    name: String,
    condition: Box<dyn Fn(&MetricDataPoint) -> bool>,
    severity: AlertSeverity,
    cooldown: Duration,
    channels: Vec<String>,
}

enum AlertSeverity {
    Critical,
    Warning,
    Info,
}

struct NotificationChannel {
    name: String,
    channel_type: ChannelType,
    configuration: HashMap<String, String>,
    enabled: bool,
}

enum ChannelType {
    Email,
    Webhook,
    Slack,
    Discord,
    Log,
}

struct Alert {
    id: String,
    rule_name: String,
    severity: AlertSeverity,
    message: String,
    workspace_id: Option<String>,
    triggered_at: chrono::DateTime<chrono::Utc>,
    resolved_at: Option<chrono::DateTime<chrono::Utc>>,
    acknowledged: bool,
}

struct SuppressionRule {
    name: String,
    condition: Box<dyn Fn(&Alert) -> bool>,
    duration: Duration,
    reason: String,
}

struct PerformanceAnalyzer {
    analyzers: Vec<PerformanceAnalyzer>,
    baseline_metrics: BaselineMetrics,
    anomaly_detector: AnomalyDetector,
    trend_analyzer: TrendAnalyzer,
}

struct BaselineMetrics {
    cpu_baseline: f64,
    memory_baseline: u64,
    response_time_baseline: Duration,
    throughput_baseline: f64,
    last_updated: chrono::DateTime<chrono::Utc>,
}

struct AnomalyDetector {
    detection_algorithm: DetectionAlgorithm,
    sensitivity: f64,
    window_size: Duration,
    minimum_data_points: usize,
}

enum DetectionAlgorithm {
    StatisticalThreshold,
    MovingAverage,
    SeasonalDecomposition,
    MachineLearning,
}

struct TrendAnalyzer {
    trend_window: Duration,        // 7 days
    trend_algorithms: Vec<TrendAlgorithm>,
    prediction_horizon: Duration,  // 24 hours
}

enum TrendAlgorithm {
    LinearRegression,
    ExponentialSmoothing,
    ARIMA,
    Prophet,
}

struct SchedulingPolicies {
    resource_allocation: ResourceAllocationPolicy,
    priority_scheduling: PrioritySchedulingPolicy,
    fair_share: FairSharePolicy,
    quality_of_service: QoSPolicy,
}

struct ResourceAllocationPolicy {
    default_cpu_limit: f64,         // 2.0 cores
    default_memory_limit: u64,      // 4GB
    default_disk_limit: u64,        // 20GB
    
    max_cpu_per_workspace: f64,     // 8.0 cores
    max_memory_per_workspace: u64,  // 16GB
    max_disk_per_workspace: u64,    // 100GB
    
    cpu_overcommit_ratio: f64,      // 1.5
    memory_overcommit_ratio: f64,   // 1.2
    
    resource_request_policies: HashMap<ProjectType, ResourceLimits>,
}

struct PrioritySchedulingPolicy {
    enable_preemption: bool,        // false
    priority_levels: u8,            // 4
    priority_aging: bool,           // true
    aging_interval: Duration,       // 1 hour
    starvation_prevention: Duration, // 4 hours
}

struct FairSharePolicy {
    enabled: bool,                  // true
    user_quotas: HashMap<String, UserQuota>,
    team_quotas: HashMap<String, TeamQuota>,
    global_limits: GlobalLimits,
}

struct UserQuota {
    max_workspaces: u32,
    max_cpu: f64,
    max_memory: u64,
    max_disk: u64,
    priority_weight: f64,
}

struct TeamQuota {
    max_workspaces: u32,
    max_cpu: f64,
    max_memory: u64,
    max_disk: u64,
    shared_resources: bool,
}

struct GlobalLimits {
    max_total_workspaces: u32,      // 100
    max_total_cpu: f64,             // 64.0 cores
    max_total_memory: u64,          // 256GB
    max_total_disk: u64,            // 2TB
    
    reserved_cpu: f64,              // 4.0 cores for system
    reserved_memory: u64,           // 8GB for system
    reserved_disk: u64,             // 100GB for system
}

struct QoSPolicy {
    service_classes: HashMap<String, ServiceClass>,
    sla_targets: SLATargets,
    degradation_policies: DegradationPolicies,
}

struct ServiceClass {
    name: String,
    cpu_guarantee: f64,
    memory_guarantee: u64,
    disk_guarantee: u64,
    network_guarantee: u64,
    
    response_time_target: Duration,
    availability_target: f64,       // 99.9%
    
    preemption_policy: PreemptionPolicy,
}

enum PreemptionPolicy {
    Never,
    LowerPriorityOnly,
    SameOrLowerPriority,
    Always,
}

```rust
const COMPREHENSIVE_HEADER_TEMPLATE: &str = r#"# - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
##@Version           :  {{VERSION_TIMESTAMP}}-git
# @@Author           :  {{NAME}}
# @@Contact          :  {{EMAIL}}
# @@License          :  {{LICENSE}}
# @@ReadME           :  {{FILE}} --help
# @@Copyright        :  {{COPYRIGHT}} {{COMPANY}}
# @@Created          :  {{TIMESTAMP}}
# @@File             :  {{FILE}}
# @@Description      :  ${1:Description}
# @@Changelog        :  ${2:}
# @@TODO             :  ${3:}
# @@Other            :  ${4:}
# @@Resource         :  ${5:}
# @@Terminal App     :  ${6|yes,no|}
# @@sudo/root        :  ${7|yes,no|}
# @@Template         :  ${8:}
# - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

${0}"#;

// Blog post template
const BLOG_POST_TEMPLATE: &str = r#"---
title: ${1:Title}
date: "{{DATE_YYYY_MM_DD}}"
author: "{{NAME}}"
tags: [blog,${2:tags}]
categories: [personal,${3:category}]
comments: true
---

${4:Content}

"#;
```

### 3.3 Template Engine
```rust
struct TemplateEngine {
    file_templates: HashMap<String, FileTemplate>,
    template_processor: TemplateProcessor,
    template_paths: Vec<PathBuf>,
    auto_initialize: bool,
}

struct FileTemplate {
    name: String,
    file_patterns: Vec<String>,
    content: String,
    variables: Vec<String>,
    language: Option<String>,
}

// Built-in file templates by extension
const FILE_TEMPLATES: &[(&str, &str)] = &[
    ("rs", r#"//! {{FILE}} - {{PROJECT}}
//! 
//! {{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
//! Created: {{TIMESTAMP}}
//! Author: {{NAME}} <{{EMAIL}}>

{{CURSOR}}
"#),

    ("py", r#"#!/usr/bin/env python3
"""{{FILE}} - {{PROJECT}}

{{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
Created: {{TIMESTAMP}}
Author: {{NAME}} <{{EMAIL}}>
License: {{LICENSE}}
"""

{{CURSOR}}
"#),

    ("js", r#"/**
 * {{FILE}} - {{PROJECT}}
 * 
 * {{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
 * Created: {{TIMESTAMP}}
 * Author: {{NAME}} <{{EMAIL}}>
 * License: {{LICENSE}}
 */

{{CURSOR}}
"#),

    ("ts", r#"/**
 * {{FILE}} - {{PROJECT}}
 * 
 * {{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
 * Created: {{TIMESTAMP}}
 * Author: {{NAME}} <{{EMAIL}}>
 * License: {{LICENSE}}
 */

{{CURSOR}}
"#),

    ("html", r#"<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta name="author" content="{{NAME}}">
    <title>{{FILE}}</title>
    <!-- 
    {{FILE}} - {{PROJECT}}
    {{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
    Author: {{NAME}} <{{EMAIL}}>
    -->
</head>
<body>
    {{CURSOR}}
</body>
</html>
"#),

    ("css", r#"/**
 * {{FILE}} - {{PROJECT}}
 * 
 * {{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
 * Created: {{TIMESTAMP}}
 * Author: {{NAME}} <{{EMAIL}}>
 * License: {{LICENSE}}
 */

{{CURSOR}}
"#),

    ("scss", r#"/**
 * {{FILE}} - {{PROJECT}}
 * 
 * {{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
 * Created: {{TIMESTAMP}}
 * Author: {{NAME}} <{{EMAIL}}>
 * License: {{LICENSE}}
 */

{{CURSOR}}
"#),

    ("go", r#"// {{FILE}} - {{PROJECT}}
// 
// {{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
// Created: {{TIMESTAMP}}
// Author: {{NAME}} <{{EMAIL}}>
// License: {{LICENSE}}

package main

{{CURSOR}}
"#),

    ("java", r#"/**
 * {{FILE}} - {{PROJECT}}
 * 
 * {{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
 * Created: {{TIMESTAMP}}
 * Author: {{NAME}} <{{EMAIL}}>
 * License: {{LICENSE}}
 */

public class {{CLASS}} {
    {{CURSOR}}
}
"#),

    ("php", r#"<?php
/**
 * {{FILE}} - {{PROJECT}}
 * 
 * {{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
 * Created: {{TIMESTAMP}}
 * Author: {{NAME}} <{{EMAIL}}>
 * License: {{LICENSE}}
 */

{{CURSOR}}
"#),

    ("c", r#"/**
 * {{FILE}} - {{PROJECT}}
 * 
 * {{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
 * Created: {{TIMESTAMP}}
 * Author: {{NAME}} <{{EMAIL}}>
 * License: {{LICENSE}}
 */

#include <stdio.h>

{{CURSOR}}
"#),

    ("cpp", r#"/**
 * {{FILE}} - {{PROJECT}}
 * 
 * {{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
 * Created: {{TIMESTAMP}}
 * Author: {{NAME}} <{{EMAIL}}>
 * License: {{LICENSE}}
 */

#include <iostream>

{{CURSOR}}
"#),

    ("h", r#"/**
 * {{FILE}} - {{PROJECT}}
 * 
 * {{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
 * Created: {{TIMESTAMP}}
 * Author: {{NAME}} <{{EMAIL}}>
 * License: {{LICENSE}}
 */

#ifndef {{MACRO_GUARD}}_H
#define {{MACRO_GUARD}}_H

{{CURSOR}}

#endif /* {{MACRO_GUARD}}_H */
"#),

    ("hpp", r#"/**
 * {{FILE}} - {{PROJECT}}
 * 
 * {{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
 * Created: {{TIMESTAMP}}
 * Author: {{NAME}} <{{EMAIL}}>
 * License: {{LICENSE}}
 */

#ifndef {{MACRO_GUARD}}_HPP
#define {{MACRO_GUARD}}_HPP

{{CURSOR}}

#endif /* {{MACRO_GUARD}}_HPP */
"#),

    ("sh", r#"#!/bin/bash
# {{FILE}} - {{PROJECT}}
# 
# {{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
# Created: {{TIMESTAMP}}
# Author: {{NAME}} <{{EMAIL}}>
# License: {{LICENSE}}

set -euo pipefail

{{CURSOR}}
"#),

    ("bash", r#"#!/bin/bash
# {{FILE}} - {{PROJECT}}
# 
# {{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
# Created: {{TIMESTAMP}}
# Author: {{NAME}} <{{EMAIL}}>
# License: {{LICENSE}}

set -euo pipefail

{{CURSOR}}
"#),

    ("zsh", r#"#!/bin/zsh
# {{FILE}} - {{PROJECT}}
# 
# {{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
# Created: {{TIMESTAMP}}
# Author: {{NAME}} <{{EMAIL}}>
# License: {{LICENSE}}

{{CURSOR}}
"#),

    ("md", r#"# {{CLASS}}

{{CURSOR}}

---

*{{PROJECT}}*  
*{{COPYRIGHT}} {{YEAR}}, {{COMPANY}}*  
*Author: {{NAME}} <{{EMAIL}}>*
"#),

    ("yaml", r#"# {{FILE}} - {{PROJECT}}
# {{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
# Created: {{TIMESTAMP}}
# Author: {{NAME}} <{{EMAIL}}>

{{CURSOR}}
"#),

    ("yml", r#"# {{FILE}} - {{PROJECT}}
# {{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
# Created: {{TIMESTAMP}}
# Author: {{NAME}} <{{EMAIL}}>

{{CURSOR}}
"#),

    ("json", r#"{
  "_comment": "{{FILE}} - {{PROJECT}}",
  "_author": "{{NAME}} <{{EMAIL}}>",
  "_created": "{{TIMESTAMP}}",
  {{CURSOR}}
}
"#),

    ("xml", r#"<?xml version="1.0" encoding="UTF-8"?>
<!--
{{FILE}} - {{PROJECT}}
{{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
Created: {{TIMESTAMP}}
Author: {{NAME}} <{{EMAIL}}>
-->

{{CURSOR}}
"#),

    ("sql", r#"-- {{FILE}} - {{PROJECT}}
-- {{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
-- Created: {{TIMESTAMP}}
-- Author: {{NAME}} <{{EMAIL}}>

{{CURSOR}}
"#),

    ("dockerfile", r#"# {{FILE}} - {{PROJECT}}
# {{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
# Created: {{TIMESTAMP}}
# Author: {{NAME}} <{{EMAIL}}>

FROM alpine:latest

{{CURSOR}}
"#),

    ("makefile", r#"# {{FILE}} - {{PROJECT}}
# {{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
# Created: {{TIMESTAMP}}
# Author: {{NAME}} <{{EMAIL}}>

.PHONY: all clean

all:
	{{CURSOR}}

clean:
	rm -f *.o

"#),

    ("gitignore", r#"# {{FILE}} - {{PROJECT}}
# {{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
# Created: {{TIMESTAMP}}

# Dependencies
node_modules/
target/
build/
dist/

# Logs
*.log
logs/

# Environment
.env
.env.local

# IDE
.vscode/
.idea/
*.swp
*.swo

# OS
.DS_Store
Thumbs.db

{{CURSOR}}
"#),

    ("toml", r#"# {{FILE}} - {{PROJECT}}
# {{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
# Created: {{TIMESTAMP}}
# Author: {{NAME}} <{{EMAIL}}>

{{CURSOR}}
"#),

    ("ini", r#"# {{FILE}} - {{PROJECT}}
# {{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
# Created: {{TIMESTAMP}}
# Author: {{NAME}} <{{EMAIL}}>

{{CURSOR}}
"#),

    ("conf", r#"# {{FILE}} - {{PROJECT}}
# {{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
# Created: {{TIMESTAMP}}
# Author: {{NAME}} <{{EMAIL}}>

{{CURSOR}}
"#),

    ("rb", r#"#!/usr/bin/env ruby
# {{FILE}} - {{PROJECT}}
# 
# {{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
# Created: {{TIMESTAMP}}
# Author: {{NAME}} <{{EMAIL}}>
# License: {{LICENSE}}

{{CURSOR}}
"#),

    ("pl", r#"#!/usr/bin/env perl
# {{FILE}} - {{PROJECT}}
# 
# {{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
# Created: {{TIMESTAMP}}
# Author: {{NAME}} <{{EMAIL}}>
# License: {{LICENSE}}

use strict;
use warnings;

{{CURSOR}}
"#),

    ("lua", r#"#!/usr/bin/env lua
-- {{FILE}} - {{PROJECT}}
-- 
-- {{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
-- Created: {{TIMESTAMP}}
-- Author: {{NAME}} <{{EMAIL}}>
-- License: {{LICENSE}}

{{CURSOR}}
"#),

    ("vim", r#"\" {{FILE}} - {{PROJECT}}
\" {{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
\" Created: {{TIMESTAMP}}
\" Author: {{NAME}} <{{EMAIL}}>

{{CURSOR}}
"#),

    ("tex", r#"% {{FILE}} - {{PROJECT}}
% {{COPYRIGHT}} {{YEAR}}, {{COMPANY}}
% Created: {{TIMESTAMP}}
% Author: {{NAME}} <{{EMAIL}}>

\documentclass{article}

\begin{document}

{{CURSOR}}

\end{document}
"#),
];
```

---

## 4. Container Management

### 4.1 Built-in Container Runtime
```rust
struct BuiltinContainerRuntime {
    runtime_engine: ContainerEngine,
    image_manager: ImageManager,
    network_manager: NetworkManager,
    storage_manager: StorageManager,
    resource_manager: ResourceManager,
}

struct ContainerEngine {
    runtime_type: RuntimeType::Builtin,
    oci_compliance: true,
    runtime_path: PathBuf,
    container_root: PathBuf,
    state_dir: PathBuf,
}

enum RuntimeType {
    Builtin,    // Our embedded runtime (youki/runc)
}

struct ImageManager {
    image_store: PathBuf,
    registry_cache: HashMap<String, RegistryInfo>,
    layer_cache: HashMap<String, LayerInfo>,
    manifest_cache: HashMap<String, ManifestInfo>,
    pull_strategies: PullStrategies,
}

struct PullStrategies {
    always: false,          // Only pull if not present locally
    if_not_present: true,   // Default strategy
    never: false,           // Use local only
}

struct RegistryInfo {
    url: String,
    auth: Option<RegistryAuth>,
    tls_verify: bool,
    timeout: Duration,
}

struct RegistryAuth {
    username: String,
    password: String,
    token: Option<String>,
}

// Default registry configuration
const DEFAULT_REGISTRIES: &[(&str, &str)] = &[
    ("docker.io", "https://index.docker.io/v1/"),
    ("ghcr.io", "https://ghcr.io"),
    ("quay.io", "https://quay.io"),
    ("gcr.io", "https://gcr.io"),
    ("registry.gitlab.com", "https://registry.gitlab.com"),
];

struct NetworkManager {
    bridge_name: String,
    subnet: ipnet::Ipv4Net,
    port_allocator: PortAllocator,
    dns_config: DnsConfig,
    firewall_rules: FirewallRules,
}

struct PortAllocator {
    port_range: std::ops::Range<u16>,
    allocated_ports: HashSet<u16>,
    reservations: HashMap<String, u16>, // container_id -> port
}

struct DnsConfig {
    nameservers: Vec<std::net::IpAddr>,
    search_domains: Vec<String>,
    options: Vec<String>,
}

struct FirewallRules {
    allow_outbound: bool,
    allow_inbound_ports: Vec<u16>,
    blocked_networks: Vec<ipnet::IpNet>,
    rate_limiting: Option<RateLimit>,
}

struct StorageManager {
    storage_driver: StorageDriver::Overlay2,
    storage_root: PathBuf,
    max_size: Option<u64>,
    cleanup_policy: CleanupPolicy,
}

enum StorageDriver {
    Overlay2,   // Default and most efficient
    Vfs,        // Fallback for systems without overlay support
}

struct CleanupPolicy {
    max_age: Duration,              // Default: 7 days
    max_total_size: Option<u64>,    // Default: 10GB
    keep_recent: usize,             // Default: 5 images
    aggressive_cleanup: bool,        // Default: false
}

struct ResourceManager {
    cgroup_version: CgroupVersion,
    default_limits: ResourceLimits,
    enforcement: ResourceEnforcement,
}

enum CgroupVersion {
    V1,
    V2,     // Preferred if available
}

struct ResourceLimits {
    cpu_limit: CpuLimit,
    memory_limit: MemoryLimit,
    disk_limit: DiskLimit,
    network_limit: NetworkLimit,
    process_limit: ProcessLimit,
}

struct CpuLimit {
    max_cores: f64,                 // Default: 2.0
    cpu_shares: Option<u64>,        // Default: 1024
    cpu_quota: Option<u64>,         // Default: None
    cpu_period: Option<u64>,        // Default: None
}

struct MemoryLimit {
    max_memory: u64,                // Default: 4GB
    swap_limit: Option<u64>,        // Default: None (no swap)
    oom_kill_disable: bool,         // Default: false
    memory_swappiness: Option<u64>, // Default: None
}

struct DiskLimit {
    max_size: u64,                  // Default: 20GB
    max_inodes: Option<u64>,        // Default: None
    device_read_bps: Option<u64>,   // Default: None
    device_write_bps: Option<u64>,  // Default: None
}

struct NetworkLimit {
    max_bandwidth: Option<u64>,     // Default: None
    max_connections: Option<u64>,   // Default: None
    allowed_ports: Vec<u16>,        // Default: 3000-9000
}

struct ProcessLimit {
    max_processes: u64,             // Default: 1024
    max_files: u64,                 // Default: 65536
}

struct ResourceEnforcement {
    strict_limits: true,            // Enforce all limits strictly
    oom_score_adj: i32,            // Default: 500
    nice_level: i32,               // Default: 0
    ionice_class: Option<u32>,     // Default: None
    ionice_priority: Option<u32>,  // Default: None
}
```

### 4.2 Container Lifecycle Management
```rust
struct ContainerLifecycleManager {
    containers: HashMap<String, ManagedContainer>,
    lifecycle_hooks: LifecycleHooks,
    health_checker: HealthChecker,
    auto_cleanup: AutoCleanup,
}

struct ManagedContainer {
    id: String,
    name: String,
    image: String,
    status: ContainerStatus,
    config: ContainerConfig,
    runtime_info: RuntimeInfo,
    resource_usage: ResourceUsage,
    health_status: HealthStatus,
    created_at: chrono::DateTime<chrono::Utc>,
    last_activity: chrono::DateTime<chrono::Utc>,
}

enum ContainerStatus {
    Creating,
    Running,
    Paused,
    Stopped,
    Sleeping,
    Hibernating,
    Failed,
    Terminated,
}

struct RuntimeInfo {
    pid: Option<u32>,
    exit_code: Option<i32>,
    started_at: Option<chrono::DateTime<chrono::Utc>>,
    finished_at: Option<chrono::DateTime<chrono::Utc>>,
    restart_count: u32,
}

struct ResourceUsage {
    cpu_usage: CpuUsage,
    memory_usage: MemoryUsage,
    disk_usage: DiskUsage,
    network_usage: NetworkUsage,
    last_updated: chrono::DateTime<chrono::Utc>,
}

struct CpuUsage {
    usage_percent: f64,
    total_usage: Duration,
    throttled_time: Duration,
}

struct MemoryUsage {
    usage_bytes: u64,
    max_usage: u64,
    cache_bytes: u64,
    swap_usage: u64,
}

struct DiskUsage {
    read_bytes: u64,
    write_bytes: u64,
    read_ops: u64,
    write_ops: u64,
}

struct NetworkUsage {
    rx_bytes: u64,
    tx_bytes: u64,
    rx_packets: u64,
    tx_packets: u64,
}

struct HealthStatus {
    status: Health,
    last_check: chrono::DateTime<chrono::Utc>,
    failure_count: u32,
    check_command: Option<String>,
}

enum Health {
    Healthy,
    Unhealthy,
    Starting,
    Unknown,
}

struct LifecycleHooks {
    pre_create: Vec<Hook>,
    post_create: Vec<Hook>,
    pre_start: Vec<Hook>,
    post_start: Vec<Hook>,
    pre_stop: Vec<Hook>,
    post_stop: Vec<Hook>,
    pre_remove: Vec<Hook>,
    post_remove: Vec<Hook>,
}

struct Hook {
    name: String,
    command: Vec<String>,
    timeout: Duration,
    fail_on_error: bool,
}

struct HealthChecker {
    check_interval: Duration,       // Default: 30 seconds
    timeout: Duration,              // Default: 10 seconds
    retries: u32,                   // Default: 3
    start_period: Duration,         // Default: 60 seconds
}

struct AutoCleanup {
    enabled: bool,                  // Default: true
    cleanup_interval: Duration,     // Default: 1 hour
    max_stopped_age: Duration,      // Default: 24 hours
    max_failed_age: Duration,       // Default: 1 hour
    keep_last_n: usize,            // Default: 3
}
```

### 4.3 Project Configuration Support
```rust
struct ProjectConfigManager {
    config_resolvers: Vec<Box<dyn ConfigResolver>>,
    project_detector: ProjectDetector,
    config_cache: HashMap<String, ProjectConfig>,
}

trait ConfigResolver {
    fn name(&self) -> &str;
    fn priority(&self) -> u8;
    fn can_resolve(&self, path: &Path) -> bool;
    fn resolve(&self, path: &Path) -> Result<ProjectConfig>;
}

// DevContainer resolver (highest priority)
struct DevContainerResolver;

impl ConfigResolver for DevContainerResolver {
    fn name(&self) -> &str { "devcontainer" }
    fn priority(&self) -> u8 { 255 }
    
    fn can_resolve(&self, path: &Path) -> bool {
        path.join(".devcontainer/devcontainer.json").exists() ||
        path.join(".devcontainer.json").exists()
    }
    
    fn resolve(&self, path: &Path) -> Result<ProjectConfig> {
        let config_path = if path.join(".devcontainer/devcontainer.json").exists() {
            path.join(".devcontainer/devcontainer.json")
        } else {
            path.join(".devcontainer.json")
        };
        
        let content = std::fs::read_to_string(&config_path)?;
        let devcontainer: DevContainerSpec = serde_json::from_str(&content)?;
        
        Ok(ProjectConfig::from_devcontainer(devcontainer))
    }
}

#[derive(Deserialize)]
struct DevContainerSpec {
    name: Option<String>,
    image: Option<String>,
    dockerfile: Option<String>,
    docker_compose_file: Option<String>,
    service: Option<String>,
    
    // Build configuration
    build: Option<BuildConfig>,
    
    // Runtime configuration
    settings: Option<serde_json::Value>,
    extensions: Option<Vec<String>>,
    features: Option<HashMap<String, serde_json::Value>>,
    
    // Container configuration
    forwarded_ports: Option<Vec<u16>>,
    port_attributes: Option<HashMap<String, PortAttribute>>,
    mounts: Option<Vec<String>>,
    
    // Lifecycle
    on_create_command: Option<String>,
    update_content_command: Option<String>,
    post_create_command: Option<String>,
    post_start_command: Option<String>,
    post_attach_command: Option<String>,
    
    // User configuration
    remote_user: Option<String>,
    container_user: Option<String>,
    user_env_probe: Option<String>,
    
    // Environment
    remote_env: Option<HashMap<String, String>>,
    container_env: Option<HashMap<String, String>>,
    
    // Advanced
    privileged: Option<bool>,
    cap_add: Option<Vec<String>>,
    security_opt: Option<Vec<String>>,
    init: Option<bool>,
    
    // Workspace
    workspace_folder: Option<String>,
    workspace_mount: Option<String>,
    
    // Other
    shutdown_action: Option<String>,
    overrides_command: Option<bool>,
    run_args: Option<Vec<String>>,
}

#[derive(Deserialize)]
struct BuildConfig {
    dockerfile: Option<String>,
    context: Option<String>,
    args: Option<HashMap<String, String>>,
    target: Option<String>,
    cache_from: Option<Vec<String>>,
}

#[derive(Deserialize)]
struct PortAttribute {
    label: Option<String>,
    on_auto_forward: Option<String>,
    elevate_if_needed: Option<bool>,
    require_local_port: Option<bool>,
}

// Gitpod resolver (medium priority)
struct GitpodResolver;

impl ConfigResolver for GitpodResolver {
    fn name(&self) -> &str { "gitpod" }
    fn priority(&self) -> u8 { 128 }
    
    fn can_resolve(&self, path: &Path) -> bool {
        path.join(".gitpod.yml").exists() ||
        path.join(".gitpod.yaml").exists()
    }
    
    fn resolve(&self, path: &Path) -> Result<ProjectConfig> {
        let config_path = if path.join(".gitpod.yml").exists() {
            path.join(".gitpod.yml")
        } else {
            path.join(".gitpod.yaml")
        };
        
        let content = std::fs::read_to_string(&config_path)?;
        let gitpod: GitpodSpec = serde_yaml::from_str(&content)?;
        
        Ok(ProjectConfig::from_gitpod(gitpod))
    }
}

#[derive(Deserialize)]
struct GitpodSpec {
    image: Option<String>,
    
    // Tasks
    tasks: Option<Vec<GitpodTask>>,
    
    // Ports
    ports: Option<Vec<GitpodPort>>,
    
    // GitHub integration
    github: Option<GitpodGitHub>,
    
    // IDE configuration
    vscode: Option<GitpodVSCode>,
    jetbrains: Option<GitpodJetBrains>,
    
    // Workspace configuration
    workspace: Option<GitpodWorkspace>,
    
    // Git configuration
    git_config: Option<HashMap<String, String>>,
    
    // Experimental features
    experimental: Option<GitpodExperimental>,
}

#[derive(Deserialize)]
struct GitpodTask {
    name: Option<String>,
    init: Option<String>,
    before: Option<String>,
    command: Option<String>,
    env: Option<HashMap<String, String>>,
    open_in: Option<String>,
    open_mode: Option<String>,
}

#[derive(Deserialize)]
struct GitpodPort {
    port: u16,
    on_open: Option<String>,
    visibility: Option<String>,
    name: Option<String>,
    description: Option<String>,
    protocol: Option<String>,
}

#[derive(Deserialize)]
struct GitpodGitHub {
    prebuilds: Option<GitpodPrebuilds>,
}

#[derive(Deserialize)]
struct GitpodPrebuilds {
    master: Option<bool>,
    branches: Option<bool>,
    pull_requests: Option<bool>,
    pull_requests_from_forks: Option<bool>,
    add_comment: Option<bool>,
    add_badge: Option<bool>,
    add_label: Option<bool>,
}

#[derive(Deserialize)]
struct GitpodVSCode {
    extensions: Option<Vec<String>>,
}

#[derive(Deserialize)]
struct GitpodJetBrains {
    intellij: Option<GitpodJetBrainsIDE>,
    goland: Option<GitpodJetBrainsIDE>,
    pycharm: Option<GitpodJetBrainsIDE>,
    phpstorm: Option<GitpodJetBrainsIDE>,
    webstorm: Option<GitpodJetBrainsIDE>,
    rider: Option<GitpodJetBrainsIDE>,
    clion: Option<GitpodJetBrainsIDE>,
    rubymine: Option<GitpodJetBrainsIDE>,
}

#[derive(Deserialize)]
struct GitpodJetBrainsIDE {
    plugins: Option<Vec<String>>,
    preferences: Option<HashMap<String, serde_json::Value>>,
}

#[derive(Deserialize)]
struct GitpodWorkspace {
    checkout_location: Option<String>,
}

#[derive(Deserialize)]
struct GitpodExperimental {
    network_policies: Option<Vec<NetworkPolicy>>,
}

#[derive(Deserialize)]
struct NetworkPolicy {
    name: String,
    egress: Option<Vec<EgressRule>>,
}

#[derive(Deserialize)]
struct EgressRule {
    to: Option<Vec<String>>,
    ports: Option<Vec<u16>>,
}

// Editor config resolver (lower priority)
struct EditorConfigResolver;

impl ConfigResolver for EditorConfigResolver {
    fn name(&self) -> &str { "editor_config" }
    fn priority(&self) -> u8 { 64 }
    
    fn can_resolve(&self, path: &Path) -> bool {
        path.join(".vscode/settings.json").exists() ||
        path.join(".editor/settings.json").exists()
    }
    
    fn resolve(&self, path: &Path) -> Result<ProjectConfig> {
        let config_path = if path.join(".vscode/settings.json").exists() {
            path.join(".vscode/settings.json")
        } else {
            path.join(".editor/settings.json")
        };
        
        let content = std::fs::read_to_string(&config_path)?;
        let settings: EditorSettings = serde_json::from_str(&content)?;
        
        Ok(ProjectConfig::from_editor_settings(settings))
    }
}

#[derive(Deserialize)]
struct EditorSettings {
    // Editor configuration
    #[serde(rename = "editor.fontSize")]
    editor_font_size: Option<u32>,
    
    #[serde(rename = "editor.fontFamily")]
    editor_font_family: Option<String>,
    
    #[serde(rename = "editor.tabSize")]
    editor_tab_size: Option<u32>,
    
    #[serde(rename = "editor.insertSpaces")]
    editor_insert_spaces: Option<bool>,
    
    #[serde(rename = "editor.wordWrap")]
    editor_word_wrap: Option<String>,
    
    #[serde(rename = "editor.formatOnSave")]
    editor_format_on_save: Option<bool>,
    
    // Language-specific settings
    #[serde(flatten)]
    language_settings: HashMap<String, serde_json::Value>,
    
    // Extensions and features
    extensions: Option<Vec<String>>,
    features: Option<HashMap<String, bool>>,
    
    // Terminal settings
    #[serde(rename = "terminal.integrated.shell.linux")]
    terminal_shell_linux: Option<String>,
    
    #[serde(rename = "terminal.integrated.shell.osx")]
    terminal_shell_osx: Option<String>,
    
    #[serde(rename = "terminal.integrated.shell.windows")]
    terminal_shell_windows: Option<String>,
    
    #[serde(rename = "terminal.integrated.fontSize")]
    terminal_font_size: Option<u32>,
    
    // Git settings
    #[serde(rename = "git.autofetch")]
    git_autofetch: Option<bool>,
    
    #[serde(rename = "git.enableSmartCommit")]
    git_enable_smart_commit: Option<bool>,
    
    // Other settings
    #[serde(flatten)]
    other: HashMap<String, serde_json::Value>,
}

// Auto-detection resolver (lowest priority)
struct AutoDetectionResolver;

impl ConfigResolver for AutoDetectionResolver {
    fn name(&self) -> &str { "auto_detection" }
    fn priority(&self) -> u8 { 1 }
    
    fn can_resolve(&self, _path: &Path) -> bool {
        true // Always can provide fallback
    }
    
    fn resolve(&self, path: &Path) -> Result<ProjectConfig> {
        let project_type = ProjectDetector::detect(path);
        Ok(ProjectConfig::from_project_type(project_type))
    }
}

struct ProjectDetector;

impl ProjectDetector {
    fn detect(path: &Path) -> ProjectType {
        // Language-specific files (order matters - more specific first)
        if path.join("Cargo.toml").exists() {
            return ProjectType::Rust;
        }
        
        if path.join("package.json").exists() {
            if path.join("tsconfig.json").exists() {
                return ProjectType::TypeScript;
            }
            return ProjectType::JavaScript;
        }
        
        if path.join("go.mod").exists() || path.join("go.sum").exists() {
            return ProjectType::Go;
        }
        
        if path.join("pom.xml").exists() || 
           path.join("build.gradle").exists() || 
           path.join("build.gradle.kts").exists() {
            return ProjectType::Java;
        }
        
        if path.join("requirements.txt").exists() ||
           path.join("pyproject.toml").exists() ||
           path.join("setup.py").exists() ||
           path.join("Pipfile").exists() {
            return ProjectType::Python;
        }
        
        if path.join("composer.json").exists() {
            return ProjectType::PHP;
        }
        
        if path.join("Gemfile").exists() {
            return ProjectType::Ruby;
        }
        
        if path.join("mix.exs").exists() {
            return ProjectType::Elixir;
        }
        
        if path.join("pubspec.yaml").exists() {
            return ProjectType::Dart;
        }
        
        if path.join("stack.yaml").exists() || 
           path.join("cabal.project").exists() {
            return ProjectType::Haskell;
        }
        
        if path.join("shard.yml").exists() {
            return ProjectType::Crystal;
        }
        
        if path.join("dub.json").exists() || 
           path.join("dub.sdl").exists() {
            return ProjectType::D;
        }
        
        if path.join("Project.toml").exists() {
            return ProjectType::Julia;
        }
        
        if path.join("elm.json").exists() {
            return ProjectType::Elm;
        }
        
        if path.join("nim.cfg").exists() ||
           self.has_nim_files(path) {
            return ProjectType::Nim;
        }
        
        if path.join("zig.json").exists() ||
           self.has_zig_files(path) {
            return ProjectType::Zig;
        }
        
        // Framework-specific detection
        if path.join("next.config.js").exists() ||
           path.join("next.config.ts").exists() {
            return ProjectType::NextJS;
        }
        
        if path.join("nuxt.config.js").exists() ||
           path.join("nuxt.config.ts").exists() {
            return ProjectType::NuxtJS;
        }
        
        if path.join("svelte.config.js").exists() {
            return ProjectType::Svelte;
        }
        
        if path.join("vue.config.js").exists() ||
           self.has_vue_files(path) {
            return ProjectType::Vue;
        }
        
        if path.join("angular.json").exists() {
            return ProjectType::Angular;
        }
        
        if path.join("ember-cli-build.js").exists() {
            return ProjectType::Ember;
        }
        
        // Build system detection
        if path.join("CMakeLists.txt").exists() {
            return ProjectType::CMake;
        }
        
        if path.join("Makefile").exists() ||
           path.join("makefile").exists() {
            return ProjectType::Make;
        }
        
        if path.join("meson.build").exists() {
            return ProjectType::Meson;
        }
        
        // Container/deployment detection
        if path.join("Dockerfile").exists() {
            return ProjectType::Docker;
        }
        
        if path.join("docker-compose.yml").exists() ||
           path.join("docker-compose.yaml").exists() {
            return ProjectType::DockerCompose;
        }
        
        if path.join("kubernetes").is_dir() ||
           path.join("k8s").is_dir() ||
           self.has_k8s_files(path) {
            return ProjectType::Kubernetes;
        }
        
        // Static site generators
        if path.join("_config.yml").exists() {
            return ProjectType::Jekyll;
        }
        
        if path.join("gatsby-config.js").exists() {
            return ProjectType::Gatsby;
        }
        
        if path.join("hugo.toml").exists() ||
           path.join("config.toml").exists() && path.join("content").is_dir() {
            return ProjectType::Hugo;
        }
        
        // Documentation
        if path.join("mkdocs.yml").exists() {
            return ProjectType::MkDocs;
        }
        
        if path.join("Sphinx").is_dir() ||
           path.join("conf.py").exists() && path.join("source").is_dir() {
            return ProjectType::Sphinx;
        }
        
        // Mobile development
        if path.join("android").is_dir() && path.join("ios").is_dir() {
            if path.join("lib").is_dir() && path.join("pubspec.yaml").exists() {
                return ProjectType::Flutter;
            }
            return ProjectType::ReactNative;
        }
        
        if path.join("app.json").exists() && path.join("App.js").exists() {
            return ProjectType::ReactNative;
        }
        
        // Web-specific
        if self.has_html_files(path) {
            if self.has_css_files(path) || self.has_js_files(path) {
                return ProjectType::WebApp;
            }
            return ProjectType::StaticSite;
        }
        
        // Fallback based on file extensions
        if self.has_c_files(path) {
            return ProjectType::C;
        }
        
        if self.has_cpp_files(path) {
            return ProjectType::Cpp;
        }
        
        if self.has_cs_files(path) {
            return ProjectType::CSharp;
        }
        
        if self.has_lua_files(path) {
            return ProjectType::Lua;
        }
        
        if self.has_perl_files(path) {
            return ProjectType::Perl;
        }
        
        if self.has_shell_files(path) {
            return ProjectType::Shell;
        }
        
        // Default
        ProjectType::Generic
    }
    
    fn has_nim_files(&self, path: &Path) -> bool {
        self.find_files_with_extension(path, "nim").len() > 0
    }
    
    fn has_zig_files(&self, path: &Path) -> bool {
        self.find_files_with_extension(path, "zig").len() > 0
    }
    
    fn has_vue_files(&self, path: &Path) -> bool {
        self.find_files_with_extension(path, "vue").len() > 0
    }
    
    fn has_k8s_files(&self, path: &Path) -> bool {
        let k8s_extensions = ["yaml", "yml"];
        for ext in &k8s_extensions {
            let files = self.find_files_with_extension(path, ext);
            for file in files {
                if let Ok(content) = std::fs::read_to_string(&file) {
                    if content.contains("apiVersion:") && content.contains("kind:") {
                        return true;
                    }
                }
            }
        }
        false
    }
    
    fn has_html_files(&self, path: &Path) -> bool {
        self.find_files_with_extension(path, "html").len() > 0
    }
    
    fn has_css_files(&self, path: &Path) -> bool {
        let css_extensions = ["css", "scss", "sass", "less"];
        css_extensions.iter().any(|ext| {
            self.find_files_with_extension(path, ext).len() > 0
        })
    }
    
    fn has_js_files(&self, path: &Path) -> bool {
        let js_extensions = ["js", "ts", "jsx", "tsx"];
        js_extensions.iter().any(|ext| {
            self.find_files_with_extension(path, ext).len() > 0
        })
    }
    
    fn has_c_files(&self, path: &Path) -> bool {
        let c_extensions = ["c", "h"];
        c_extensions.iter().any(|ext| {
            self.find_files_with_extension(path, ext).len() > 0
        })
    }
    
    fn has_cpp_files(&self, path: &Path) -> bool {
        let cpp_extensions = ["cpp", "cxx", "cc", "hpp", "hxx", "hh"];
        cpp_extensions.iter().any(|ext| {
            self.find_files_with_extension(path, ext).len() > 0
        })
    }
    
    fn has_cs_files(&self, path: &Path) -> bool {
        self.find_files_with_extension(path, "cs").len() > 0
    }
    
    fn has_lua_files(&self, path: &Path) -> bool {
        self.find_files_with_extension(path, "lua").len() > 0
    }
    
    fn has_perl_files(&self, path: &Path) -> bool {
        let perl_extensions = ["pl", "pm"];
        perl_extensions.iter().any(|ext| {
            self.find_files_with_extension(path, ext).len() > 0
        })
    }
    
    fn has_shell_files(&self, path: &Path) -> bool {
        let shell_extensions = ["sh", "bash", "zsh", "fish"];
        shell_extensions.iter().any(|ext| {
            self.find_files_with_extension(path, ext).len() > 0
        })
    }
    
    fn find_files_with_extension(&self, path: &Path, extension: &str) -> Vec<PathBuf> {
        let mut files = Vec::new();
        if let Ok(entries) = std::fs::read_dir(path) {
            for entry in entries.flatten() {
                let path = entry.path();
                if path.is_file() {
                    if let Some(ext) = path.extension() {
                        if ext == extension {
                            files.push(path);
                        }
                    }
                } else if path.is_dir() && !self.should_skip_directory(&path) {
                    files.extend(self.find_files_with_extension(&path, extension));
                }
            }
        }
        files
    }
    
    fn should_skip_directory(&self, path: &Path) -> bool {
        let skip_dirs = [
            "node_modules", ".git", "target", "build", "dist", 
            ".next", ".cache", "__pycache__", ".pytest_cache",
            "vendor", ".vendor", "deps", "_build"
        ];
        
        if let Some(dir_name) = path.file_name() {
            if let Some(dir_str) = dir_name.to_str() {
                return skip_dirs.contains(&dir_str) || dir_str.starts_with('.');
            }
        }
        false
    }
}

#[derive(Debug, Clone, PartialEq)]
enum ProjectType {
    // Languages
    Rust,
    JavaScript,
    TypeScript,
    Python,
    Go,
    Java,
    PHP,
    Ruby,
    C,
    Cpp,
    CSharp,
    Elixir,
    Dart,
    Haskell,
    Crystal,
    D,
    Julia,
    Elm,
    Nim,
    Zig,
    Lua,
    Perl,
    Shell,
    
    // Frameworks
    NextJS,
    NuxtJS,
    Svelte,
    Vue,
    Angular,
    Ember,
    ReactNative,
    Flutter,
    
    // Build Systems
    CMake,
    Make,
    Meson,
    
    // Containers & Deployment
    Docker,
    DockerCompose,
    Kubernetes,
    
    // Static Site Generators
    Jekyll,
    Gatsby,
    Hugo,
    
    // Documentation
    MkDocs,
    Sphinx,
    
    // Web
    WebApp,
    StaticSite,
    
    // Generic
    Generic,
}

// Unified project configuration
struct ProjectConfig {
    project_type: ProjectType,
    container_config: ContainerConfig,
    editor_config: EditorConfig,
    tools_config: ToolsConfig,
    environment_config: EnvironmentConfig,
    tasks_config: TasksConfig,
}

impl ProjectConfig {
    fn from_devcontainer(devcontainer: DevContainerSpec) -> Self {
        // Convert DevContainer spec to unified config
        // Implementation details...
        todo!()
    }
    
    fn from_gitpod(gitpod: GitpodSpec) -> Self {
        // Convert Gitpod spec to unified config
        // Implementation details...
        todo!()
    }
    
    fn from_editor_settings(settings: EditorSettings) -> Self {
        // Convert editor settings to unified config
        // Implementation details...
        todo!()
    }
    
    fn from_project_type(project_type: ProjectType) -> Self {
        // Generate default config based on project type
        let container_config = ContainerConfig::for_project_type(&project_type);
        let editor_config = EditorConfig::for_project_type(&project_type);
        let tools_config = ToolsConfig::for_project_type(&project_type);
        let environment_config = EnvironmentConfig::for_project_type(&project_type);
        let tasks_config = TasksConfig::for_project_type(&project_type);
        
        Self {
            project_type,
            container_config,
            editor_config,
            tools_config,
            environment_config,
            tasks_config,
        }
    }
}
```

---

## 5. Embedded Scheduler

### 5.1 Scheduler Architecture
```rust
struct EmbeddedScheduler {
    workspaces: HashMap<String, WorkspaceInstance>,
    resource_manager: ResourceManager,
    scheduler_engine: SchedulerEngine,
    lifecycle_manager: LifecycleManager,
    monitoring_system: MonitoringSystem,
    policies: SchedulingPolicies,
}

struct WorkspaceInstance {
    id: String,
    name: String,
    owner_id: String,
    project_path: PathBuf,
    container: ManagedContainer,
    status: WorkspaceStatus,
    resources: ResourceAllocation,
    activity: ActivityTracker,
    created_at: chrono::DateTime<chrono::Utc>,
    last_accessed: chrono::DateTime<chrono::Utc>,
    access_count: u64,
    priority: WorkspacePriority,
}

enum WorkspaceStatus {
    Initializing,   // Creating container, installing tools
    Ready,          // Available for use
    Active,         // User currently connected
    Idle,           // No user activity for some time
    Sleeping,       // Container paused to save resources
    Hibernating,    // Container stopped, state saved
    Terminating,    // Being cleaned up
    Failed,         // Error state
}

struct ActivityTracker {
    last_file_change: Option<chrono::DateTime<chrono::Utc>>,
    last_terminal_activity: Option<chrono::DateTime<chrono::Utc>>,
    last_web_interaction: Option<chrono::DateTime<chrono::Utc>>,
    total_active_time: Duration,
    file_changes_count: u64,
    terminal_commands_count: u64,
    web_requests_count: u64,
}

enum WorkspacePriority {
    Critical,   // Never automatically terminate
    High,       // Extended idle timeouts
    Normal,     // Default scheduling
    Low,        // First to be cleaned up
}

struct SchedulerEngine {
    scheduling_queue: VecDeque<SchedulingTask>,
    resource_balancer: ResourceBalancer,
    decision_engine: DecisionEngine,
    metrics_collector: MetricsCollector,
}

struct SchedulingTask {
    task_type: TaskType,
    workspace_id: String,
    scheduled_at: chrono::DateTime<chrono::Utc>,
    priority: TaskPriority,
    retry_count: u32,
    max_retries: u32,
}

enum TaskType {
    Create,
    Start,
    Stop,
    Pause,
    Resume,
    Hibernate,
    Terminate,
    ResourceUpdate,
    HealthCheck,
    Backup,
}

enum TaskPriority {
    Critical,   // Process immediately
    High,       // Process within 1 minute
    Normal,     // Process within 5 minutes
    Low,        // Process when resources available
}

struct ResourceBalancer {
    balancing_strategy: BalancingStrategy,
    resource_thresholds: ResourceThresholds,
    auto_scaling: AutoScalingConfig,
}

enum BalancingStrategy {
    RoundRobin,
    LeastLoaded,
    PriorityBased,
    ActivityBased,
}

struct ResourceThresholds {
    cpu_high_threshold: f64,        // 80%
    cpu_low_threshold: f64,         // 20%
    memory_high_threshold: f64,     // 85%
    memory_low_threshold: f64,      // 30%
    disk_high_threshold: f64,       // 90%
    disk_low_threshold: f64,        // 50%
    network_high_threshold: f64,    // 75%
}

struct AutoScalingConfig {
    enabled: bool,
    scale_up_cooldown: Duration,    // 5 minutes
    scale_down_cooldown: Duration,  // 15 minutes
    cpu_scale_up_threshold: f64,    // 70%
    cpu_scale_down_threshold: f64,  // 30%
    memory_scale_up_threshold: f64, // 80%
    memory_scale_down_threshold: f64, // 40%
}

struct DecisionEngine {
    decision_rules: Vec<DecisionRule>,
    machine_learning_model: Option<MLModel>,
    historical_data: HistoricalData,
}

struct DecisionRule {
    name: String,
    condition: Box<dyn Fn(&WorkspaceInstance, &SystemMetrics) -> bool>,
    action: SchedulingAction,
    weight: f64,
}

enum SchedulingAction {
    NoAction,
    Sleep,
    Hibernate,
    Terminate,
    IncreaseResources,
    DecreaseResources,
    ChangePriority(WorkspacePriority),
}

struct MLModel {
    model_type: ModelType,
    model_data: Vec<u8>,
    last_trained: chrono::DateTime<chrono::Utc>,
    accuracy: f64,
}

enum ModelType {
    LinearRegression,
    DecisionTree,
    NeuralNetwork,
    XGBoost,
}

struct HistoricalData {
    workspace_usage_patterns: HashMap<String, UsagePattern>,
    resource_utilization: Vec<ResourceDataPoint>,
    performance_metrics: Vec<PerformanceDataPoint>,
    retention_period: Duration, // 30 days
}

struct UsagePattern {
    user_id: String,
    typical_session_duration: Duration,
    preferred_work_hours: TimeRange,
    common_project_types: Vec<ProjectType>,
    resource_usage_profile: ResourceProfile,
}

struct TimeRange {
    start_hour: u8,
    end_hour: u8,
    timezone: chrono_tz::Tz,
}

struct ResourceProfile {
    average_cpu_usage: f64,
    peak_cpu_usage: f64,
    average_memory_usage: u64,
    peak_memory_usage: u64,
    network_intensity: NetworkIntensity,
}

enum NetworkIntensity {
    Low,    // Occasional git operations
    Medium, // Regular API calls, package downloads
    High,   // Streaming, large file transfers
}

struct ResourceDataPoint {
    timestamp: chrono::DateTime<chrono::Utc>,
    cpu_usage: f64,
    memory_usage: u64,
    disk_usage: u64,
    network_rx: u64,
    network_tx: u64,
    active_workspaces: u32,
}

struct PerformanceDataPoint {
    timestamp: chrono::DateTime<chrono::Utc>,
    response_time: Duration,
    throughput: f64,
    error_rate: f64,
    user_satisfaction: Option<f64>,
}

struct LifecycleManager {
    lifecycle_policies: LifecyclePolicies,
    state_machine: WorkspaceStateMachine,
    transition_handlers: HashMap<StateTransition, TransitionHandler>,
}

struct LifecyclePolicies {
    idle_timeout: Duration,         // 30 minutes
    sleep_timeout: Duration,        // 2 hours
    hibernation_timeout: Duration,  // 24 hours
    termination_timeout: Duration,  // 7 days
    
    priority_multipliers: HashMap<WorkspacePriority, f64>,
    project_type_multipliers: HashMap<ProjectType, f64>,
    
    backup_before_hibernation: bool,
    backup_before_termination: bool,
    
    grace_period: Duration,         // 5 minutes warning before action
    force_termination_timeout: Duration, // 30 seconds
}

struct WorkspaceStateMachine {
    states: HashMap<WorkspaceStatus, StateDefinition>,
    transitions: HashMap<StateTransition, TransitionDefinition>,
}

struct StateDefinition {
    name: String,
    description: String,
    entry_actions: Vec<Action>,
    exit_actions: Vec<Action>,
    allowed_transitions: Vec<WorkspaceStatus>,
    timeout: Option<Duration>,
    timeout_transition: Option<WorkspaceStatus>,
}

struct StateTransition {
    from: WorkspaceStatus,
    to: WorkspaceStatus,
}

struct TransitionDefinition {
    name: String,
    conditions: Vec<Condition>,
    actions: Vec<Action>,
    duration: Option<Duration>,
    rollback_actions: Vec<Action>,
}

struct TransitionHandler {
    handler: Box<dyn Fn(&mut WorkspaceInstance) -> Result<()>>,
    timeout: Duration,
    retry_policy: RetryPolicy,
}

struct Action {
    name: String,
    handler: Box<dyn Fn(&mut WorkspaceInstance) -> Result<()>>,
    timeout: Duration,
    required: bool,
}

struct Condition {
    name: String,
    checker: Box<dyn Fn(&WorkspaceInstance) -> bool>,
}

struct RetryPolicy {
    max_retries: u32,
    base_delay: Duration,
    max_delay: Duration,
    backoff_multiplier: f64,
}

struct MonitoringSystem {
    metrics_collector: MetricsCollector,
    health_checker: HealthChecker,
    alerting_system: AlertingSystem,
    performance_analyzer: PerformanceAnalyzer,
}

struct MetricsCollector {
    collection_interval: Duration,  // 30 seconds
    metrics_buffer: Vec<MetricDataPoint>,
    aggregation_window: Duration,   // 5 minutes
    retention_period: Duration,     // 30 days
}

struct MetricDataPoint {
    timestamp: chrono::DateTime<chrono::Utc>,
    workspace_id: String,
    metric_type: MetricType,
    value: f64,
    tags: HashMap<String, String>,
}

enum MetricType {
    CpuUsage,
    MemoryUsage,
    DiskUsage,
    NetworkRx,
    NetworkTx,
    ResponseTime,
    RequestCount,
    ErrorCount,
    UserSatisfaction,
    Custom(String),
}

struct HealthChecker {
    health_checks: Vec<HealthCheck>,
    check_interval: Duration,       // 1 minute
    failure_threshold: u32,         // 3 consecutive failures
    recovery_threshold: u32,        // 2 consecutive successes
}

struct HealthCheck {
    name: String,
    checker: Box<dyn Fn(&WorkspaceInstance) -> Result<HealthStatus>>,
    timeout: Duration,
    critical: bool,
}

struct AlertingSystem {
    alert_rules: Vec<AlertRule>,
    notification_channels: Vec<NotificationChannel>,
    alert_history: Vec<Alert>,
    suppression_rules: Vec<SuppressionRule>,
}

struct AlertRule {
    name: String,
    condition: Box<dyn Fn(&MetricDataPoint) -> bool>,
    severity: AlertSeverity,
    cooldown: Duration,
    channels: Vec<String>,
}

enum AlertSeverity {
    Critical,
    Warning,
    Info,
}

struct NotificationChannel {
    name: String,
    channel_type: ChannelType,
    configuration: HashMap<String, String>,
    enabled: bool,
}

enum ChannelType {
    Email,
    Webhook,
    Slack,
    Discord,
    Log,
}

struct Alert {
    id: String,
    rule_name: String,
    severity: AlertSeverity,
    message: String,
    workspace_id: Option<String>,
    triggered_at: chrono::DateTime<chrono::Utc>,
    resolved_at: Option<chrono::DateTime<chrono::Utc>>,
    acknowledged: bool,
}

struct SuppressionRule {
    name: String,
    condition: Box<dyn Fn(&Alert) -> bool>,
    duration: Duration,
    reason: String,
}

struct PerformanceAnalyzer {
    analyzers: Vec<PerformanceAnalyzer>,
    baseline_metrics: BaselineMetrics,
    anomaly_detector: AnomalyDetector,
    trend_analyzer: TrendAnalyzer,
}

struct BaselineMetrics {
    cpu_baseline: f64,
    memory_baseline: u64,
    response_time_baseline: Duration,
    throughput_baseline: f64,
    last_updated: chrono::DateTime<chrono::Utc>,
}

struct AnomalyDetector {
    detection_algorithm: DetectionAlgorithm,
    sensitivity: f64,
    window_size: Duration,
    minimum_data_points: usize,
}

enum DetectionAlgorithm {
    StatisticalThreshold,
    MovingAverage,
    SeasonalDecomposition,
    MachineLearning,
}

struct TrendAnalyzer {
    trend_window: Duration,        // 7 days
    trend_algorithms: Vec<TrendAlgorithm>,
    prediction_horizon: Duration,  // 24 hours
}

enum TrendAlgorithm {
    LinearRegression,
    ExponentialSmoothing,
    ARIMA,
    Prophet,
}

struct SchedulingPolicies {
    resource_allocation: ResourceAllocationPolicy,
    priority_scheduling: PrioritySchedulingPolicy,
    fair_share: FairSharePolicy,
    quality_of_service: QoSPolicy,
}

struct ResourceAllocationPolicy {
    default_cpu_limit: f64,         // 2.0 cores
    default_memory_limit: u64,      // 4GB
    default_disk_limit: u64,        // 20GB
    
    max_cpu_per_workspace: f64,     // 8.0 cores
    max_memory_per_workspace: u64,  // 16GB
    max_disk_per_workspace: u64,    // 100GB
    
    cpu_overcommit_ratio: f64,      // 1.5
    memory_overcommit_ratio: f64,   // 1.2
    
    resource_request_policies: HashMap<ProjectType, ResourceLimits>,
}

struct PrioritySchedulingPolicy {
    enable_preemption: bool,        // false
    priority_levels: u8,            // 4
    priority_aging: bool,           // true
    aging_interval: Duration,       // 1 hour
    starvation_prevention: Duration, // 4 hours
}

struct FairSharePolicy {
    enabled: bool,                  // true
    user_quotas: HashMap<String, UserQuota>,
    team_quotas: HashMap<String, TeamQuota>,
    global_limits: GlobalLimits,
}

struct UserQuota {
    max_workspaces: u32,
    max_cpu: f64,
    max_memory: u64,
    max_disk: u64,
    priority_weight: f64,
}

struct TeamQuota {
    max_workspaces: u32,
    max_cpu: f64,
    max_memory: u64,
    max_disk: u64,
    shared_resources: bool,
}

struct GlobalLimits {
    max_total_workspaces: u32,      // 100
    max_total_cpu: f64,             // 64.0 cores
    max_total_memory: u64,          // 256GB
    max_total_disk: u64,            // 2TB
    
    reserved_cpu: f64,              // 4.0 cores for system
    reserved_memory: u64,           // 8GB for system
    reserved_disk: u64,             // 100GB for system
}

struct QoSPolicy {
    service_classes: HashMap<String, ServiceClass>,
    sla_targets: SLATargets,
    degradation_policies: DegradationPolicies,
}

struct ServiceClass {
    name: String,
    cpu_guarantee: f64,
    memory_guarantee: u64,
    disk_guarantee: u64,
    network_guarantee: u64,
    
    response_time_target: Duration,
    availability_target: f64,       // 99.9%
    
    preemption_policy: PreemptionPolicy,
}

enum PreemptionPolicy {
    Never,
    LowerPriorityOnly,
    SameOrLowerPriority,
    Always,
}

```rust
struct SLATargets {
    workspace_start_time: Duration,     // 30 seconds
    file_operation_latency: Duration,   // 100ms
    terminal_response_time: Duration,   // 50ms
    code_completion_latency: Duration,  // 200ms
    build_start_time: Duration,         // 5 seconds
    availability_percentage: f64,       // 99.9%
    data_durability: f64,              // 99.99%
}

struct DegradationPolicies {
    graceful_degradation: bool,         // true
    feature_toggles: HashMap<String, bool>,
    performance_limits: PerformanceLimits,
    emergency_procedures: EmergencyProcedures,
}

struct PerformanceLimits {
    max_concurrent_builds: u32,         // 5
    max_file_size: u64,                // 100MB
    max_project_size: u64,             // 10GB
    request_rate_limit: u32,           // 1000/minute
    concurrent_sessions_per_user: u32,  // 3
}

struct EmergencyProcedures {
    enable_read_only_mode: bool,
    disable_new_workspaces: bool,
    force_cleanup_idle: bool,
    reduce_resource_limits: bool,
    enable_emergency_notifications: bool,
}
```

---

## 6. Configuration System

### 6.1 Unified Configuration Manager
```rust
struct ConfigManager {
    database: ConfigDatabase,
    defaults: HardcodedDefaults,
    cache: ConfigCache,
    validators: ConfigValidators,
    migrations: ConfigMigrations,
}

struct ConfigDatabase {
    connection_pool: SqlitePool,
    database_path: PathBuf,
    schema_version: u32,
    encryption: Option<ConfigEncryption>,
}

struct ConfigEncryption {
    algorithm: EncryptionAlgorithm,
    key_derivation: KeyDerivation,
    encrypted_fields: HashSet<String>,
}

enum EncryptionAlgorithm {
    ChaCha20Poly1305,
    AES256GCM,
}

struct KeyDerivation {
    algorithm: KDFAlgorithm,
    salt: Vec<u8>,
    iterations: u32,
}

enum KDFAlgorithm {
    Argon2id,
    PBKDF2,
    Scrypt,
}

struct ConfigCache {
    cached_values: HashMap<String, CachedValue>,
    cache_ttl: Duration,            // 5 minutes
    max_cache_size: usize,          // 1000 entries
    invalidation_strategy: InvalidationStrategy,
}

struct CachedValue {
    value: serde_json::Value,
    cached_at: chrono::DateTime<chrono::Utc>,
    access_count: u64,
    last_accessed: chrono::DateTime<chrono::Utc>,
}

enum InvalidationStrategy {
    TTL,
    LRU,
    WriteThrough,
    WriteBack,
}

struct ConfigValidators {
    validators: HashMap<String, Box<dyn ConfigValidator>>,
    validation_rules: ValidationRules,
}

trait ConfigValidator {
    fn validate(&self, key: &str, value: &serde_json::Value) -> Result<(), ValidationError>;
    fn sanitize(&self, key: &str, value: serde_json::Value) -> serde_json::Value;
}

struct ValidationRules {
    required_fields: HashSet<String>,
    type_constraints: HashMap<String, ValueType>,
    range_constraints: HashMap<String, RangeConstraint>,
    pattern_constraints: HashMap<String, regex::Regex>,
    custom_validators: HashMap<String, Box<dyn Fn(&serde_json::Value) -> bool>>,
}

enum ValueType {
    String,
    Integer,
    Float,
    Boolean,
    Array,
    Object,
}

struct RangeConstraint {
    min: Option<f64>,
    max: Option<f64>,
    exclusive_min: bool,
    exclusive_max: bool,
}

struct ConfigMigrations {
    migrations: Vec<ConfigMigration>,
    current_version: u32,
    target_version: u32,
}

struct ConfigMigration {
    version: u32,
    description: String,
    up: Box<dyn Fn(&mut SqliteConnection) -> Result<()>>,
    down: Box<dyn Fn(&mut SqliteConnection) -> Result<()>>,
}

// Complete default configuration
struct HardcodedDefaults {
    server: ServerConfig,
    security: SecurityConfig,
    editor: EditorConfig,
    terminal: TerminalConfig,
    container: ContainerConfig,
    scheduler: SchedulerConfig,
    monitoring: MonitoringConfig,
    templates: TemplateConfig,
    features: FeatureConfig,
    ui: UIConfig,
    storage: StorageConfig,
    networking: NetworkingConfig,
    logging: LoggingConfig,
    performance: PerformanceConfig,
    backup: BackupConfig,
    integrations: IntegrationsConfig,
}

struct ServerConfig {
    // Network configuration
    host: String,                   // "0.0.0.0"
    port: u16,                      // 8080
    https_port: Option<u16>,        // 8443
    bind_address: Option<String>,   // None
    
    // Server limits
    max_connections: u32,           // 1000
    max_request_size: u64,          // 100MB
    request_timeout: Duration,      // 30 seconds
    keep_alive_timeout: Duration,   // 60 seconds
    
    // Session configuration
    session_timeout: Duration,      // 24 hours
    session_cleanup_interval: Duration, // 1 hour
    max_sessions_per_user: u32,     // 5
    
    // Workspace limits
    max_workspaces: u32,            // 10
    max_workspaces_per_user: u32,   // 3
    workspace_idle_timeout: Duration, // 30 minutes
    
    // Performance
    worker_threads: Option<u32>,    // Auto-detect
    max_blocking_threads: u32,      // 512
    thread_stack_size: Option<usize>, // Default
    
    // Features
    enable_metrics: bool,           // true
    enable_health_checks: bool,     // true
    enable_admin_api: bool,         // true
    enable_user_registration: bool, // false
    
    // Development
    debug_mode: bool,              // false
    log_requests: bool,            // true
    cors_enabled: bool,            // true
    cors_origins: Vec<String>,     // ["*"]
}

struct SecurityConfig {
    // Authentication
    auth_methods: AuthMethods,
    password_policy: PasswordPolicy,
    session_security: SessionSecurity,
    api_security: ApiSecurity,
    
    // Authorization
    rbac_enabled: bool,            // true
    default_user_role: String,     // "user"
    admin_role: String,            // "admin"
    
    // Encryption
    encryption_at_rest: bool,      // true
    encryption_in_transit: bool,   // true
    tls_config: TlsConfig,
    
    // Security headers
    security_headers: SecurityHeaders,
    content_security_policy: ContentSecurityPolicy,
    
    // Rate limiting
    rate_limiting: RateLimiting,
    
    // Audit
    audit_logging: bool,           // true
    audit_retention: Duration,     // 90 days
    
    // Compliance
    gdpr_compliance: bool,         // true
    data_retention_policy: DataRetentionPolicy,
}

struct AuthMethods {
    local_auth: bool,              // true
    ldap_auth: Option<LdapConfig>,
    oauth2_auth: Option<OAuth2Config>,
    saml_auth: Option<SamlConfig>,
    api_key_auth: bool,            // true
    
    multi_factor_auth: Option<MfaConfig>,
    single_sign_on: Option<SsoConfig>,
}

struct PasswordPolicy {
    min_length: usize,             // 12
    max_length: usize,             // 128
    require_uppercase: bool,       // true
    require_lowercase: bool,       // true
    require_numbers: bool,         // true
    require_symbols: bool,         // true
    
    password_history: usize,       // 5
    password_expiry: Option<Duration>, // None
    account_lockout_threshold: u32, // 5
    account_lockout_duration: Duration, // 30 minutes
    
    banned_passwords: Vec<String>, // Common weak passwords
    allow_username_in_password: bool, // false
}

struct SessionSecurity {
    secure_cookies: bool,          // true
    same_site_policy: SameSitePolicy, // Strict
    session_rotation: bool,        // true
    csrf_protection: bool,         // true
    xss_protection: bool,          // true
    
    ip_binding: bool,              // false
    user_agent_binding: bool,      // false
    concurrent_session_limit: u32, // 5
}

enum SameSitePolicy {
    Strict,
    Lax,
    None,
}

struct ApiSecurity {
    api_key_length: usize,         // 32
    api_key_expiry: Option<Duration>, // 90 days
    rate_limit_per_key: u32,       // 1000/hour
    
    webhook_verification: bool,     // true
    webhook_timeout: Duration,      // 30 seconds
    
    cors_enabled: bool,            // true
    allowed_origins: Vec<String>,   // []
    allowed_methods: Vec<String>,   // ["GET", "POST", "PUT", "DELETE"]
    allowed_headers: Vec<String>,   // ["Authorization", "Content-Type"]
}

struct TlsConfig {
    min_version: TlsVersion,       // TLSv1.2
    max_version: TlsVersion,       // TLSv1.3
    cipher_suites: Vec<String>,    // Secure defaults
    protocols: Vec<String>,        // ["h2", "http/1.1"]
    
    certificate_source: CertificateSource,
    auto_redirect_http: bool,      // true
    hsts_enabled: bool,            // true
    hsts_max_age: Duration,        // 31536000 seconds (1 year)
}

enum TlsVersion {
    TLSv1_0,
    TLSv1_1,
    TLSv1_2,
    TLSv1_3,
}

enum CertificateSource {
    LetsEncrypt(LetsEncryptConfig),
    SelfSigned,
    Custom(CustomCertConfig),
}

struct LetsEncryptConfig {
    challenge_method: ChallengeMethod,
    domain: String,
    email: String,
    staging: bool,                 // false for production
    auto_renewal: bool,            // true
    renewal_threshold: Duration,   // 30 days
    
    dns_provider: Option<DnsProvider>,
    dns_credentials: Option<HashMap<String, String>>,
}

enum ChallengeMethod {
    Http01,
    Dns01,
    TlsAlpn01,
}

enum DnsProvider {
    Cloudflare,
    Route53,
    DigitalOcean,
    GoDaddy,
    Namecheap,
    RFC2136,
}

struct CustomCertConfig {
    cert_file: PathBuf,
    key_file: PathBuf,
    ca_file: Option<PathBuf>,
    
    auto_reload: bool,             // true
    check_interval: Duration,      // 24 hours
}

struct SecurityHeaders {
    x_frame_options: Option<String>, // "DENY"
    x_content_type_options: Option<String>, // "nosniff"
    x_xss_protection: Option<String>, // "1; mode=block"
    referrer_policy: Option<String>, // "strict-origin-when-cross-origin"
    permissions_policy: Option<String>,
    
    custom_headers: HashMap<String, String>,
}

struct ContentSecurityPolicy {
    enabled: bool,                 // true
    default_src: Vec<String>,      // ["'self'"]
    script_src: Vec<String>,       // ["'self'", "'unsafe-inline'"]
    style_src: Vec<String>,        // ["'self'", "'unsafe-inline'"]
    img_src: Vec<String>,          // ["'self'", "data:", "https:"]
    font_src: Vec<String>,         // ["'self'"]
    connect_src: Vec<String>,      // ["'self'", "ws:", "wss:"]
    media_src: Vec<String>,        // ["'self'"]
    object_src: Vec<String>,       // ["'none'"]
    frame_src: Vec<String>,        // ["'none'"]
    worker_src: Vec<String>,       // ["'self'"]
    
    report_uri: Option<String>,
    report_only: bool,             // false
}

struct RateLimiting {
    enabled: bool,                 // true
    global_limit: u32,             // 10000/hour
    per_ip_limit: u32,             // 1000/hour
    per_user_limit: u32,           // 5000/hour
    
    burst_size: u32,               // 100
    window_size: Duration,         // 1 hour
    
    exempted_ips: Vec<String>,     // []
    exempted_users: Vec<String>,   // []
    
    rate_limit_headers: bool,      // true
    block_on_limit: bool,          // true
    log_violations: bool,          // true
}

struct DataRetentionPolicy {
    user_data_retention: Duration, // 2 years
    log_retention: Duration,       // 90 days
    audit_retention: Duration,     // 7 years
    session_retention: Duration,   // 30 days
    
    automatic_cleanup: bool,       // true
    cleanup_interval: Duration,    // 24 hours
    
    backup_before_deletion: bool,  // true
    secure_deletion: bool,         // true
}

struct EditorConfig {
    // Core editor settings
    theme: String,                 // "dark"
    font_size: u32,               // 12
    font_family: String,          // "'Fira Code', 'Hack Nerd Font', 'Consolas', 'Courier New', monospace"
    font_ligatures: bool,         // true
    font_weight: String,          // "normal"
    line_height: f32,             // 1.4
    
    // Editing behavior
    tab_size: u32,                // 2
    insert_spaces: bool,          // true
    trim_trailing_whitespace: bool, // true
    insert_final_newline: bool,   // true
    trim_final_newlines: bool,    // true
    
    // Word wrapping
    word_wrap: String,            // "wordWrapColumn"
    word_wrap_column: u32,        // 160
    wrapping_indent: String,      // "indent"
    
    // Line numbers and gutter
    line_numbers: String,         // "on"
    line_numbers_min_chars: u32,  // 3
    gutter_decorations_width: u32, // 10
    fold_gutter_width: u32,       // 10
    
    // Cursor and selection
    cursor_blinking: String,      // "expand"
    cursor_smooth_caret_animation: bool, // true
    cursor_width: u32,            // 2
    selection_highlight: bool,    // true
    
    // Indentation and guides
    show_indent_guides: bool,     // true
    indent_guides_style: String,  // "rainbow"
    bracket_pair_colorization: bool, // true
    bracket_pair_guides: String,  // "active"
    
    // Whitespace and rendering
    render_whitespace: String,    // "boundary"
    render_control_characters: bool, // false
    render_line_highlight: String, // "line"
    render_final_newline: String, // "on"
    
    // Minimap
    minimap_enabled: bool,        // false
    minimap_side: String,         // "right"
    minimap_size: String,         // "proportional"
    minimap_show_slider: String,  // "mouseover"
    
    // Scrolling
    scroll_beyond_last_line: bool, // true
    scroll_beyond_last_column: u32, // 5
    smooth_scrolling: bool,       // true
    mouse_wheel_zoom: bool,       // true
    
    // Auto-save and formatting
    auto_save: String,            // "onWindowChange"
    auto_save_delay: u32,         // 1000 (milliseconds)
    format_on_save: bool,         // true
    format_on_paste: bool,        // true
    format_on_type: bool,         // false
    
    // Code intelligence
    quick_suggestions: bool,      // true
    quick_suggestions_delay: u32, // 10 (milliseconds)
    parameter_hints: bool,        // true
    suggest_on_trigger_characters: bool, // true
    accept_suggestion_on_commit_character: bool, // true
    accept_suggestion_on_enter: String, // "on"
    tab_completion: String,       // "on"
    
    // Hover and peek
    hover_enabled: bool,          // true
    hover_delay: u32,             // 300 (milliseconds)
    hover_sticky: bool,           // true
    
    // Links and navigation
    links: bool,                  // true
    goto_location_multiple_type_definitions: String, // "peek"
    goto_location_multiple_declarations: String, // "peek"
    goto_location_multiple_implementations: String, // "peek"
    goto_location_multiple_references: String, // "peek"
    
    // Find and replace
    find_auto_find_in_selection: String, // "never"
    find_seed_search_string_from_selection: String, // "always"
    find_global_find_clipboard: bool, // false
    
    // Multi-cursor
    multi_cursor_modifier: String, // "alt"
    multi_cursor_merge_overlapping: bool, // true
    multi_cursor_paste: String,   // "spread"
    
    // Code folding
    folding: bool,                // true
    folding_strategy: String,     // "auto"
    folding_highlight: bool,      // true
    unfold_on_click_after_end_of_line: bool, // false
    
    // Code actions and refactoring
    code_actions_on_save: HashMap<String, bool>, // {}
    code_lens: bool,              // true
    
    // Language-specific overrides
    language_overrides: HashMap<String, LanguageOverride>,
}

struct LanguageOverride {
    tab_size: Option<u32>,
    insert_spaces: Option<bool>,
    trim_trailing_whitespace: Option<bool>,
    format_on_save: Option<bool>,
    word_wrap: Option<String>,
    bracket_pair_colorization: Option<bool>,
    
    specific_settings: HashMap<String, serde_json::Value>,
}

struct TerminalConfig {
    // Font and appearance
    font_family: String,          // "monospace"
    font_size: u32,               // 12
    font_weight: String,          // "normal"
    line_height: f32,             // 1.2
    letter_spacing: f32,          // 0.0
    
    // Shell configuration
    shell_path: Option<String>,   // Auto-detect
    shell_args: Vec<String>,      // []
    environment_variables: HashMap<String, String>, // {"TERM": "xterm-256color"}
    
    // Behavior
    scrollback_lines: u32,        // 1000
    scroll_on_output: bool,       // false
    scroll_on_input: bool,        // true
    fast_scroll_modifier: String, // "alt"
    fast_scroll_sensitivity: u32, // 5
    
    // Cursor and selection
    cursor_style: String,         // "block"
    cursor_blink: bool,           // true
    cursor_width: u32,            // 1
    
    // Copy and paste
    copy_on_selection: bool,      // false
    paste_on_right_click: bool,   // true
    trim_whitespace: bool,        // true
    
    // Links and navigation
    enable_file_links: bool,      // true
    enable_web_links: bool,       // true
    link_activation_modifier: String, // "ctrl"
    
    // Bell and notifications
    enable_bell: bool,            // false
    bell_sound: bool,             // false
    bell_style: String,           // "none"
    
    // Advanced
    alt_click_moves_cursor: bool, // true
    confirm_on_exit: bool,        // false
    confirm_on_kill: bool,        // true
    enable_persistent_sessions: bool, // false
    
    // Integration
    integrated_terminal_tabs: bool, // true
    max_terminals: u32,           // 10
    terminal_cwd: String,         // "inherited"
    
    // Accessibility
    screen_reader_mode: bool,     // false
    accessibility_support: String, // "auto"
}

struct ContainerConfig {
    // Runtime configuration
    runtime_type: String,         // "builtin"
    runtime_path: Option<PathBuf>, // None (embedded)
    state_directory: PathBuf,     // "/var/lib/caspod/containers"
    
    // Default container settings
    default_image: String,        // "alpine:latest"
    default_user: String,         // "developer"
    default_working_dir: String,  // "/workspace"
    default_shell: String,        // "/bin/sh"
    
    // Resource limits
    default_cpu_limit: f64,       // 2.0
    default_memory_limit: String, // "4g"
    default_disk_limit: String,   // "20g"
    
    max_cpu_limit: f64,           // 8.0
    max_memory_limit: String,     // "16g"
    max_disk_limit: String,       // "100g"
    
    // Network configuration
    network_mode: String,         // "bridge"
    dns_servers: Vec<String>,     // ["8.8.8.8", "8.8.4.4"]
    port_range: String,           // "30000-40000"
    
    // Storage configuration
    storage_driver: String,       // "overlay2"
    storage_root: PathBuf,        // "/var/lib/caspod/storage"
    
    // Security
    enable_user_namespaces: bool, // true
    enable_seccomp: bool,         // true
    enable_apparmor: bool,        // true
    enable_selinux: bool,         // false
    
    default_capabilities: Vec<String>, // Minimal set
    dropped_capabilities: Vec<String>, // Dangerous capabilities
    
    // Performance
    enable_cgroup_v2: bool,       // true
    enable_systemd_cgroup: bool,  // true
    enable_cpu_rt_runtime: bool,  // false
    
    // Cleanup and maintenance
    auto_cleanup: bool,           // true
    cleanup_interval: Duration,   // 1 hour
    max_container_age: Duration,  // 7 days
    max_image_age: Duration,      // 30 days
    
    // Registry configuration
    default_registry: String,     // "docker.io"
    registry_mirrors: Vec<String>, // []
    insecure_registries: Vec<String>, // []
    registry_auth: HashMap<String, RegistryCredentials>,
    
    // Build configuration
    build_timeout: Duration,      // 30 minutes
    build_memory_limit: String,   // "8g"
    build_cpu_limit: f64,         // 4.0
    
    // Logging
    log_driver: String,           // "json-file"
    log_max_size: String,         // "10m"
    log_max_files: u32,           // 3
}

struct RegistryCredentials {
    username: String,
    password: String,             // Encrypted in database
    email: Option<String>,
}

struct SchedulerConfig {
    // Core scheduling
    scheduling_interval: Duration, // 30 seconds
    max_concurrent_operations: u32, // 10
    operation_timeout: Duration,   // 5 minutes
    
    // Resource management
    enable_auto_scaling: bool,     // true
    scale_up_threshold: f64,       // 80%
    scale_down_threshold: f64,     // 30%
    scale_cooldown: Duration,      // 5 minutes
    
    // Lifecycle management
    idle_detection_interval: Duration, // 1 minute
    idle_timeout: Duration,        // 30 minutes
    sleep_timeout: Duration,       // 2 hours
    hibernation_timeout: Duration, // 24 hours
    termination_timeout: Duration, // 7 days
    
    // Priority and fairness
    enable_priority_scheduling: bool, // true
    enable_fair_share: bool,       // true
    enable_preemption: bool,       // false
    
    // Health and monitoring
    health_check_interval: Duration, // 1 minute
    health_check_timeout: Duration, // 30 seconds
    health_check_retries: u32,     // 3
    
    // Performance optimization
    enable_machine_learning: bool, // false
    enable_predictive_scaling: bool, // false
    enable_workload_balancing: bool, // true
    
    // Policies
    default_workspace_priority: String, // "normal"
    max_workspaces_per_user: u32,  // 5
    max_total_workspaces: u32,     // 100
    
    // Emergency procedures
    enable_emergency_mode: bool,   // true
    emergency_cpu_threshold: f64,  // 95%
    emergency_memory_threshold: f64, // 95%
    emergency_disk_threshold: f64, // 95%
}

struct MonitoringConfig {
    // Metrics collection
    metrics_enabled: bool,         // true
    metrics_interval: Duration,    // 30 seconds
    metrics_retention: Duration,   // 7 days
    
    // Health checks
    health_checks_enabled: bool,   // true
    health_check_endpoints: Vec<String>, // ["/health", "/ready"]
    
    // Logging
    log_level: String,            // "info"
    log_format: String,           // "json"
    log_file: Option<PathBuf>,    // None (stdout)
    log_rotation: bool,           // true
    log_max_size: String,         // "100m"
    log_max_files: u32,           // 10
    
    // Alerting
    alerting_enabled: bool,       // true
    alert_thresholds: AlertThresholds,
    notification_channels: Vec<NotificationChannelConfig>,
    
    // Performance monitoring
    enable_profiling: bool,       // false
    profiling_interval: Duration, // 1 minute
    profile_retention: Duration,  // 24 hours
    
    // Tracing
    enable_tracing: bool,         // false
    tracing_endpoint: Option<String>, // None
    tracing_sample_rate: f64,     // 0.1
}

struct AlertThresholds {
    cpu_usage_warning: f64,       // 80%
    cpu_usage_critical: f64,      // 95%
    memory_usage_warning: f64,    // 85%
    memory_usage_critical: f64,   // 95%
    disk_usage_warning: f64,      // 80%
    disk_usage_critical: f64,     // 90%
    response_time_warning: Duration, // 1 second
    response_time_critical: Duration, // 5 seconds
    error_rate_warning: f64,      // 5%
    error_rate_critical: f64,     // 10%
}

struct NotificationChannelConfig {
    name: String,
    channel_type: String,         // "email", "webhook", "slack"
    enabled: bool,
    configuration: HashMap<String, String>,
    filters: Vec<String>,
}

struct TemplateConfig {
    // Template settings
    auto_initialize: bool,        // true
    template_search_paths: Vec<PathBuf>, // ["./templates", "~/.config/caspod/templates"]
    
    // Author information
    author_name: String,          // "Developer"
    author_email: String,         // "dev@company.com"
    author_hostname: String,      // Auto-detect
    
    // Organization
    company_name: String,         // "casapps"
    organization: String,         // "casapps"
    
    // Legal
    license: String,              // "MIT"
    copyright: String,            // "Copyright (c)"
    
    // Custom variables
    custom_variables: HashMap<String, String>, // {}
    
    // Template behavior
    show_template_chooser: bool,  // true
    remember_template_choice: bool, // true
    validate_templates: bool,     // true
    
    // Snippets
    enable_snippets: bool,        // true
    snippet_suggestions: bool,    // true
    snippet_tab_completion: bool, // true
}

struct FeatureConfig {
    // Editor features
    auto_pairs: bool,             // true
    multiple_cursors: bool,       // true
    smart_commenting: bool,       // true
    rainbow_brackets: bool,       // true
    auto_rename_tag: bool,        // true
    color_preview: bool,          // true
    
    // Git features
    git_gutter: bool,             // true
    git_blame: bool,              // true
    git_staging: bool,            // true
    git_diff: bool,               // true
    
    // Language features
    syntax_highlighting: bool,    // true
    code_completion: bool,        // true
    error_detection: bool,        // true
    code_formatting: bool,        // true
    code_linting: bool,           // true
    
    // Productivity features
    fuzzy_finder: bool,           // true
    symbol_search: bool,          // true
    global_search: bool,          // true
    live_server: bool,            // true
    emmet: bool,                  // true
    markdown_preview: bool,       // true
    spell_checker: bool,          // true
    
    // Terminal features
    integrated_terminal: bool,    // true
    multiple_terminals: bool,     // true
    terminal_links: bool,         // true
    
    // Debugging features
    breakpoint_support: bool,     // true
    variable_inspection: bool,    // true
    call_stack: bool,             // true
    
    // Collaboration features
    shared_workspaces: bool,      // false
    real_time_collaboration: bool, // false
    
    // Advanced features
    vim_mode: bool,               // false
    command_palette: bool,        // true
    quick_actions: bool,          // true
}

struct UIConfig {
    // Theme configuration
    default_theme: String,        // "dark"
    available_themes: Vec<String>, // ["dark", "light", "base16"]
    allow_custom_themes: bool,    // true
    
    // Layout
    sidebar_position: String,     // "right"
    activity_bar_position: String, // "left"
    panel_position: String,       // "bottom"
    
    // Responsive design
    mobile_breakpoint: u32,       // 720
    tablet_breakpoint: u32,       // 1024
    desktop_breakpoint: u32,      // 2560
    
    // Animations
    enable_animations: bool,      // true
    animation_duration: Duration, // 200ms
    reduce_motion: bool,          // false
    
    // Accessibility
    high_contrast: bool,          // false
    screen_reader_support: bool,  // true
    keyboard_navigation: bool,    // true
    focus_indicators: bool,       // true
    
    // Customization
    custom_css: Option<String>,   // None
    custom_js: Option<String>,    // None
    branding: BrandingConfig,
}

struct BrandingConfig {
    logo_url: Option<String>,     // None
    favicon_url: Option<String>,  // None
    instance_name: String,        // "Caspod"
    primary_color: Option<String>, // None
    accent_color: Option<String>, // None
    
    footer_text: Option<String>,  // None
    custom_links: Vec<CustomLink>, // []
}

struct CustomLink {
    text: String,
    url: String,
    icon: Option<String>,
    external: bool,
}

struct StorageConfig {
    // Storage backend
    storage_type: String,         // "local"
    storage_path: PathBuf,        // "/var/lib/caspod"
    
    // Limits
    max_workspace_size: String,   // "20g"
    max_total_storage: String,    // "500g"
    max_file_size: String,        // "100m"
    
    // Performance
    enable_compression: bool,     // true
    compression_algorithm: String, // "gzip"
    enable_deduplication: bool,   // false
    
    // Backup
    backup_enabled: bool,         // true
    backup_interval: Duration,    // 24 hours
    backup_retention: Duration,   // 30 days
    backup_compression: bool,     // true
    
    // Cleanup
    auto_cleanup: bool,           // true
    cleanup_interval: Duration,   // 1 hour
    temp_file_retention: Duration, // 1 hour
    
    // Encryption
    encryption_enabled: bool,     // true
    encryption_algorithm: String, // "AES-256-GCM"
    
    // Cloud storage (optional)
    cloud_storage: Option<CloudStorageConfig>,
}

struct CloudStorageConfig {
    provider: String,             // "s3", "gcs", "azure"
    bucket: String,
    region: Option<String>,
    credentials: HashMap<String, String>,
    
    sync_enabled: bool,           // false
    sync_interval: Duration,      // 1 hour
    conflict_resolution: String,  // "local_wins"
}

struct NetworkingConfig {
    // Network settings
    enable_ipv6: bool,            // true
    bind_ipv4: bool,              // true
    bind_ipv6: bool,              // true
    
    // Proxy support
    http_proxy: Option<String>,   // None
    https_proxy: Option<String>,  // None
    no_proxy: Vec<String>,        // ["localhost", "127.0.0.1"]
    
    // DNS
    dns_servers: Vec<String>,     // ["8.8.8.8", "1.1.1.1"]
    dns_timeout: Duration,        // 5 seconds
    dns_retries: u32,             // 3
    
    // Timeouts
    connect_timeout: Duration,    // 10 seconds
    read_timeout: Duration,       // 30 seconds
    write_timeout: Duration,      // 30 seconds
    
    // Limits
    max_connections: u32,         // 1000
    max_idle_connections: u32,    // 100
    idle_timeout: Duration,       // 90 seconds
    
    // Security
    enable_tcp_nodelay: bool,     // true
    enable_tcp_keepalive: bool,   // true
    keepalive_timeout: Duration,  // 60 seconds
}

struct LoggingConfig {
    // Basic logging
    level: String,                // "info"
    format: String,               // "json"
    timestamp_format: String,     // "rfc3339"
    
    // Output
    console_output: bool,         // true
    file_output: bool,            // true
    file_path: Option<PathBuf>,   // "/var/log/caspod/caspod.log"
    
    // Rotation
    rotation_enabled: bool,       // true
    max_file_size: String,        // "100m"
    max_files: u32,               // 10
    rotation_time: Option<String>, // "daily"
    
    // Filtering
    filters: Vec<LogFilter>,      // []
    sampling_rate: Option<f64>,   // None
    
    // Structured logging
    enable_structured_logging: bool, // true
    include_caller_info: bool,    // false
    include_stack_trace: bool,    // false
    
    // Performance
    async_logging: bool,          // true
    buffer_size: usize,           // 8192
    flush_interval: Duration,     // 1 second
    
    // Integration
    syslog_enabled: bool,         // false
    syslog_facility: Option<String>, // None
    remote_logging: Option<RemoteLoggingConfig>,
}

struct LogFilter {
    target: String,               // Logger name or pattern
    level: String,                // Minimum level for this target
}

struct RemoteLoggingConfig {
    endpoint: String,
    protocol: String,             // "http", "tcp", "udp"
    format: String,               // "json", "logfmt"
    batch_size: usize,            // 100
    flush_interval: Duration,     // 5 seconds
    
    auth: Option<RemoteLoggingAuth>,
}

struct RemoteLoggingAuth {
    auth_type: String,            // "basic", "bearer", "api_key"
    credentials: HashMap<String, String>,
}

struct PerformanceConfig {
    // Threading
    worker_threads: Option<u32>,  // Auto-detect
    max_blocking_threads: u32,    // 512
    thread_keep_alive: Duration,  // 10 seconds
    thread_stack_size: Option<usize>, // Default
    
    // Memory management
    enable_jemalloc: bool,        // true
    gc_threshold: usize,          // 1GB
    memory_limit: Option<String>, // None
    
    // Caching
    enable_caching: bool,         // true
    cache_size: String,           // "256m"
    cache_ttl: Duration,          // 1 hour
    
    // I/O optimization
    enable_sendfile: bool,        // true
    tcp_nodelay: bool,            // true
    tcp_cork: bool,               // false
    
    // Compression
    enable_gzip: bool,            // true
    gzip_level: u32,              // 6
    gzip_min_size: u32,           // 1024
    
    // Optimization flags
    optimize_for_size: bool,      // false
    optimize_for_speed: bool,     // true
    enable_lto: bool,             // true
    
    // Profiling
    enable_profiling: bool,       // false
    profiling_sample_rate: f64,   // 0.01
    profiling_output: Option<PathBuf>, // None
}

struct BackupConfig {
    // Backup settings
    enabled: bool,                // true
    backup_interval: Duration,    // 24 hours
    backup_time: Option<String>,  // "02:00" (2 AM)
    
    // Retention
    keep_daily: u32,              // 7
    keep_weekly: u32,             // 4
    keep_monthly: u32,            // 12
    keep_yearly: u32,             // 2
    
    // Storage
    backup_path: PathBuf,         // "/var/lib/caspod/backups"
    compression_enabled: bool,    // true
    compression_level: u32,       // 6
    encryption_enabled: bool,     // true
    
    // What to backup
    include_workspaces: bool,     // true
    include_user_data: bool,      // true
    include_system_config: bool,  // true
    include_logs: bool,           // false
    
    exclude_patterns: Vec<String>, // ["*.tmp", "node_modules", ".git"]
    
    // Verification
    verify_backups: bool,         // true
    verification_sample_rate: f64, // 0.1
    
    // Notifications
    notify_on_success: bool,      // false
    notify_on_failure: bool,      // true
    notification_email: Option<String>, // None
    
    // Remote backup
    remote_backup: Option<RemoteBackupConfig>,
}

struct RemoteBackupConfig {
    provider: String,             // "s3", "gcs", "azure", "sftp"
    endpoint: String,
    credentials: HashMap<String, String>,
    
    sync_enabled: bool,           // false
    incremental_sync: bool,       // true
    bandwidth_limit: Option<String>, // "10m"
}

struct IntegrationsConfig {
    // Version control
    git_integration: GitIntegrationConfig,
    
    // Authentication providers
    auth_providers: Vec<AuthProviderConfig>,
    
    // Notification services
    notification_services: Vec<NotificationServiceConfig>,
    
    // Container registries
    container_registries: Vec<ContainerRegistryConfig>,
    
    // Cloud platforms
    cloud_platforms: Vec<CloudPlatformConfig>,
    
    // Monitoring services
    monitoring_services: Vec<MonitoringServiceConfig>,
    
    // CI/CD platforms
    cicd_platforms: Vec<CicdPlatformConfig>,
}

struct GitIntegrationConfig {
    default_branch: String,       // "main"
    auto_fetch_interval: Duration, // 5 minutes
    enable_hooks: bool,           // true
    
    commit_signing: bool,         // false
    gpg_key_id: Option<String>,   // None
    
    merge_strategy: String,       // "merge"
    rebase_strategy: String,      // "interactive"
    
    ignore_patterns: Vec<String>, // [".caspod/"]
}

struct AuthProviderConfig {
    name: String,
    provider_type: String,        // "oauth2", "saml", "ldap"
    enabled: bool,
    configuration: HashMap<String, String>,
    
    user_mapping: UserMappingConfig,
    group_mapping: Option<GroupMappingConfig>,
}

struct UserMappingConfig {
    username_field: String,       // "email"
    email_field: String,          // "email"
    name_field: String,           // "name"
    avatar_field: Option<String>, // "picture"
}

struct GroupMappingConfig {
    groups_field: String,         // "groups"
    admin_groups: Vec<String>,    // ["admin", "administrators"]
    user_groups: Vec<String>,     // ["users", "developers"]
}

struct NotificationServiceConfig {
    name: String,
    service_type: String,         // "slack", "discord", "email", "webhook"
    enabled: bool,
    configuration: HashMap<String, String>,
    
    event_filters: Vec<String>,   // ["workspace_created", "user_login"]
}

struct ContainerRegistryConfig {
    name: String,
    url: String,
    username: Option<String>,
    password: Option<String>,     // Encrypted
    
    default_registry: bool,       // false
    insecure: bool,               // false
    mirror: bool,                 // false
}

struct CloudPlatformConfig {
    name: String,
    platform_type: String,       // "aws", "gcp", "azure"
    credentials: HashMap<String, String>,
    
    default_region: Option<String>,
    enabled_services: Vec<String>,
}

struct MonitoringServiceConfig {
    name: String,
    service_type: String,         // "prometheus", "datadog", "newrelic"
    endpoint: String,
    credentials: HashMap<String, String>,
    
    metrics_enabled: bool,        // true
    traces_enabled: bool,         // false
    logs_enabled: bool,           // false
}

struct CicdPlatformConfig {
    name: String,
    platform_type: String,       // "github_actions", "gitlab_ci", "jenkins"
    webhook_url: String,
    secret: String,               // Encrypted
    
    auto_trigger: bool,           // false
    trigger_events: Vec<String>,  // ["workspace_created", "git_push"]
}
```

## 7. Web Server & API

### 7.1 Complete API Specification
```rust
struct WebServer {
    server: HttpServer,
    router: Router,
    middleware: MiddlewareStack,
    websocket_server: WebSocketServer,
    static_handler: StaticHandler,
}

// Complete REST API
const REST_API_ROUTES: &[(&str, &str, &str)] = &[
    // Authentication & Authorization
    ("POST", "/api/auth/login", "auth_login"),
    ("POST", "/api/auth/logout", "auth_logout"),
    ("POST", "/api/auth/refresh", "auth_refresh"),
    ("GET", "/api/auth/me", "auth_me"),
    ("POST", "/api/auth/register", "auth_register"),
    ("POST", "/api/auth/forgot-password", "auth_forgot_password"),
    ("POST", "/api/auth/reset-password", "auth_reset_password"),
    ("POST", "/api/auth/change-password", "auth_change_password"),
    ("GET", "/api/auth/providers", "auth_providers"),
    ("GET", "/api/auth/oauth/{provider}", "auth_oauth_start"),
    ("GET", "/api/auth/oauth/{provider}/callback", "auth_oauth_callback"),
    
    // User Management
    ("GET", "/api/users", "users_list"),
    ("POST", "/api/users", "users_create"),
    ("GET", "/api/users/{id}", "users_get"),
    ("PUT", "/api/users/{id}", "users_update"),
    ("DELETE", "/api/users/{id}", "users_delete"),
    ("GET", "/api/users/{id}/workspaces", "users_workspaces"),
    ("GET", "/api/users/{id}/activity", "users_activity"),
    ("POST", "/api/users/{id}/disable", "users_disable"),
    ("POST", "/api/users/{id}/enable", "users_enable"),
    ("POST", "/api/users/invite", "users_invite"),
    ("GET", "/api/users/invite/{token}", "users_invite_accept"),
    
    // Workspace Management
    ("GET", "/api/workspaces", "workspaces_list"),
    ("POST", "/api/workspaces", "workspaces_create"),
    ("GET", "/api/workspaces/{id}", "workspaces_get"),
    ("PUT", "/api/workspaces/{id}", "workspaces_update"),
    ("DELETE", "/api/workspaces/{id}", "workspaces_delete"),
    ("GET", "/api/workspaces/{id}/status", "workspaces_status"),
    ("POST", "/api/workspaces/{id}/start", "workspaces_start"),
    ("POST", "/api/workspaces/{id}/stop", "workspaces_stop"),
    ("POST", "/api/workspaces/{id}/restart", "workspaces_restart"),
    ("POST", "/api/workspaces/{id}/pause", "workspaces_pause"),
    ("POST", "/api/workspaces/{id}/resume", "workspaces_resume"),
    ("POST", "/api/workspaces/{id}/hibernate", "workspaces_hibernate"),
    ("GET", "/api/workspaces/{id}/logs", "workspaces_logs"),
    ("GET", "/api/workspaces/{id}/metrics", "workspaces_metrics"),
    ("POST", "/api/workspaces/{id}/backup", "workspaces_backup"),
    ("POST", "/api/workspaces/{id}/restore", "workspaces_restore"),
    ("GET", "/api/workspaces/{id}/backups", "workspaces_backups"),
    ("POST", "/api/workspaces/{id}/clone", "workspaces_clone"),
    ("POST", "/api/workspaces/{id}/share", "workspaces_share"),
    ("DELETE", "/api/workspaces/{id}/share", "workspaces_unshare"),
    ("GET", "/api/workspaces/{id}/collaborators", "workspaces_collaborators"),
    ("POST", "/api/workspaces/{id}/collaborators", "workspaces_add_collaborator"),
    ("DELETE", "/api/workspaces/{id}/collaborators/{user_id}", "workspaces_remove_collaborator"),
    
    // File Management
    ("GET", "/api/workspaces/{id}/files", "files_list"),
    ("GET", "/api/workspaces/{id}/files/**path", "files_get"),
    ("PUT", "/api/workspaces/{id}/files/**path", "files_create_or_update"),
    ("DELETE", "/api/workspaces/{id}/files/**path", "files_delete"),
    ("POST", "/api/workspaces/{id}/files/**path/copy", "files_copy"),
    ("POST", "/api/workspaces/{id}/files/**path/move", "files_move"),
    ("POST", "/api/workspaces/{id}/files/**path/rename", "files_rename"),
    ("POST", "/api/workspaces/{id}/files/upload", "files_upload"),
    ("GET", "/api/workspaces/{id}/files/download/**path", "files_download"),
    ("POST", "/api/workspaces/{id}/files/search", "files_search"),
    ("GET", "/api/workspaces/{id}/files/**path/history", "files_history"),
    ("POST", "/api/workspaces/{id}/files/**path/restore", "files_restore"),
    
    // Terminal Management
    ("GET", "/api/workspaces/{id}/terminals", "terminals_list"),
    ("POST", "/api/workspaces/{id}/terminals", "terminals_create"),
    ("GET", "/api/workspaces/{id}/terminals/{terminal_id}", "terminals_get"),
    ("DELETE", "/api/workspaces/{id}/terminals/{terminal_id}", "terminals_delete"),
    ("POST", "/api/workspaces/{id}/terminals/{terminal_id}/resize", "terminals_resize"),
    ("POST", "/api/workspaces/{id}/terminals/{terminal_id}/input", "terminals_input"),
    ("GET", "/api/workspaces/{id}/terminals/{terminal_id}/output", "terminals_output"),
    
    // Git Operations
    ("GET", "/api/workspaces/{id}/git/status", "git_status"),
    ("POST", "/api/workspaces/{id}/git/init", "git_init"),
    ("POST", "/api/workspaces/{id}/git/clone", "git_clone"),
    ("POST", "/api/workspaces/{id}/git/add", "git_add"),
    ("POST", "/api/workspaces/{id}/git/commit", "git_commit"),
    ("POST", "/api/workspaces/{id}/git/push", "git_push"),
    ("POST", "/api/workspaces/{id}/git/pull", "git_pull"),
    ("POST", "/api/workspaces/{id}/git/fetch", "git_fetch"),
    ("GET", "/api/workspaces/{id}/git/branches", "git_branches"),
    ("POST", "/api/workspaces/{id}/git/branch", "git_create_branch"),
    ("POST", "/api/workspaces/{id}/git/checkout", "git_checkout"),
    ("POST", "/api/workspaces/{id}/git/merge", "git_merge"),
    ("POST", "/api/workspaces/{id}/git/rebase", "git_rebase"),
    ("GET", "/api/workspaces/{id}/git/log", "git_log"),
    ("GET", "/api/workspaces/{id}/git/diff", "git_diff"),
    ("POST", "/api/workspaces/{id}/git/stash", "git_stash"),
    ("GET", "/api/workspaces/{id}/git/remotes", "git_remotes"),
    ("POST", "/api/workspaces/{id}/git/remote", "git_add_remote"),
    ("DELETE", "/api/workspaces/{id}/git/remote/{name}", "git_remove_remote"),
    
    // Language Server Protocol
    ("GET", "/api/workspaces/{id}/lsp/servers", "lsp_servers"),
    ("POST", "/api/workspaces/{id}/lsp/servers/{language}/start", "lsp_start"),
    ("POST", "/api/workspaces/{id}/lsp/servers/{language}/stop", "lsp_stop"),
    ("GET", "/api/workspaces/{id}/lsp/servers/{language}/status", "lsp_status"),
    ("POST", "/api/workspaces/{id}/lsp/request", "lsp_request"),
    ("POST", "/api/workspaces/{id}/lsp/notification", "lsp_notification"),
    
    // Build & Task Management
    ("GET", "/api/workspaces/{id}/tasks", "tasks_list"),
    ("POST", "/api/workspaces/{id}/tasks", "tasks_create"),
    ("GET", "/api/workspaces/{id}/tasks/{task_id}", "tasks_get"),
    ("POST", "/api/workspaces/{id}/tasks/{task_id}/run", "tasks_run"),
    ("POST", "/api/workspaces/{id}/tasks/{task_id}/stop", "tasks_stop"),
    ("GET", "/api/workspaces/{id}/tasks/{task_id}/logs", "tasks_logs"),
    ("GET", "/api/workspaces/{id}/tasks/templates", "tasks_templates"),
    
    // Debugging
    ("GET", "/api/workspaces/{id}/debug/sessions", "debug_sessions"),
    ("POST", "/api/workspaces/{id}/debug/sessions", "debug_create_session"),
    ("DELETE", "/api/workspaces/{id}/debug/sessions/{session_id}", "debug_delete_session"),
    ("POST", "/api/workspaces/{id}/debug/sessions/{session_id}/start", "debug_start"),
    ("POST", "/api/workspaces/{id}/debug/sessions/{session_id}/stop", "debug_stop"),
    ("POST", "/api/workspaces/{id}/debug/sessions/{session_id}/pause", "debug_pause"),
    ("POST", "/api/workspaces/{id}/debug/sessions/{session_id}/continue", "debug_continue"),
    ("POST", "/api/workspaces/{id}/debug/sessions/{session_id}/step", "debug_step"),
    ("GET", "/api/workspaces/{id}/debug/sessions/{session_id}/stacktrace", "debug_stacktrace"),
    ("GET", "/api/workspaces/{id}/debug/sessions/{session_id}/variables", "debug_variables"),
    ("POST", "/api/workspaces/{id}/debug/sessions/{session_id}/breakpoints", "debug_set_breakpoint"),
    ("DELETE", "/api/workspaces/{id}/debug/sessions/{session_id}/breakpoints/{bp_id}", "debug_remove_breakpoint"),
    
    // Extensions & Features
    ("GET", "/api/features", "features_list"),
    ("GET", "/api/features/{feature}/status", "features_status"),
    ("POST", "/api/features/{feature}/enable", "features_enable"),
    ("POST", "/api/features/{feature}/disable", "features_disable"),
    ("GET", "/api/features/{feature}/config", "features_config"),
    ("PUT", "/api/features/{feature}/config", "features_update_config"),
    
    // Templates & Snippets
    ("GET", "/api/templates", "templates_list"),
    ("GET", "/api/templates/{id}", "templates_get"),
    ("POST", "/api/templates", "templates_create"),
    ("PUT", "/api/templates/{id}", "templates_update"),
    ("DELETE", "/api/templates/{id}", "templates_delete"),
    ("GET", "/api/snippets", "snippets_list"),
    ("GET", "/api/snippets/{id}", "snippets_get"),
    ("POST", "/api/snippets", "snippets_create"),
    ("PUT", "/api/snippets/{id}", "snippets_update"),
    ("DELETE", "/api/snippets/{id}", "snippets_delete"),
    ("POST", "/api/snippets/{id}/expand", "snippets_expand"),
    
    // Configuration Management
    ("GET", "/api/config", "config_get"),
    ("PUT", "/api/config", "config_update"),
    ("GET", "/api/config/schema", "config_schema"),
    ("POST", "/api/config/validate", "config_validate"),
    ("POST", "/api/config/reset", "config_reset"),
    ("GET", "/api/config/export", "config_export"),
    ("POST", "/api/config/import", "config_import"),
    
    // System Management
    ("GET", "/api/system/status", "system_status"),
    ("GET", "/api/system/info", "system_info"),
    ("GET", "/api/system/metrics", "system_metrics"),
    ("GET", "/api/system/health", "system_health"),
    ("POST", "/api/system/restart", "system_restart"),
    ("POST", "/api/system/shutdown", "system_shutdown"),
    ("GET", "/api/system/logs", "system_logs"),
    ("POST", "/api/system/backup", "system_backup"),
    ("POST", "/api/system/restore", "system_restore"),
    ("GET", "/api/system/backups", "system_backups"),
    ("POST", "/api/system/update", "system_update"),
    ("GET", "/api/system/update/status", "system_update_status"),
    
    // Container Management
    ("GET", "/api/containers", "containers_list"),
    ("GET", "/api/containers/{id}", "containers_get"),
    ("POST", "/api/containers/{id}/start", "containers_start"),
    ("POST", "/api/containers/{id}/stop", "containers_stop"),
    ("POST", "/api/containers/{id}/restart", "containers_restart"),
    ("DELETE", "/api/containers/{id}", "containers_delete"),
    ("GET", "/api/containers/{id}/logs", "containers_logs"),
    ("GET", "/api/containers/{id}/stats", "containers_stats"),
    ("POST", "/api/containers/{id}/exec", "containers_exec"),
    
    // Image Management
    ("GET", "/api/images", "images_list"),
    ("POST", "/api/images/pull", "images_pull"),
    ("DELETE", "/api/images/{id}", "images_delete"),
    ("POST", "/api/images/build", "images_build"),
    ("GET", "/api/images/{id}/history", "images_history"),
    ("POST", "/api/images/{id}/tag", "images_tag"),
    ("POST", "/api/images/prune", "images_prune"),
    
    // Monitoring & Analytics
    ("GET", "/api/metrics", "metrics_get"),
    ("GET", "/api/metrics/workspaces", "metrics_workspaces"),
    ("GET", "/api/metrics/users", "metrics_users"),
    ("GET", "/api/metrics/system", "metrics_system"),
    ("GET", "/api/analytics/usage", "analytics_usage"),
    ("GET", "/api/analytics/performance", "analytics_performance"),
    ("GET", "/api/analytics/errors", "analytics_errors"),
    
    // Notifications & Alerts
    ("GET", "/api/notifications", "notifications_list"),
    ("POST", "/api/notifications", "notifications_create"),
    ("PUT", "/api/notifications/{id}/read", "notifications_mark_read"),
    ("DELETE", "/api/notifications/{id}", "notifications_delete"),
    ("GET", "/api/alerts", "alerts_list"),
    ("GET", "/api/alerts/{id}", "alerts_get"),
    ("POST", "/api/alerts/{id}/acknowledge", "alerts_acknowledge"),
    ("POST", "/api/alerts/{id}/resolve", "alerts_resolve"),
    
    // Search
    ("POST", "/api/search/files", "search_files"),
    ("POST", "/api/search/content", "search_content"),
    ("POST", "/api/search/symbols", "search_symbols"),
    ("POST", "/api/search/workspaces", "search_workspaces"),
    ("GET", "/api/search/recent", "search_recent"),
    
    // Live Server
    ("POST", "/api/workspaces/{id}/server/start", "live_server_start"),
    ("POST", "/api/workspaces/{id}/server/stop", "live_server_stop"),
    ("GET", "/api/workspaces/{id}/server/status", "live_server_status"),
    ("PUT", "/api/workspaces/{id}/server/config", "live_server_config"),
    
    // SSL/TLS Management
    ("GET", "/api/ssl/certificates", "ssl_certificates"),
    ("POST", "/api/ssl/certificates", "ssl_create_certificate"),
    ("DELETE", "/api/ssl/certificates/{id}", "ssl_delete_certificate"),
    ("POST", "/api/ssl/certificates/{id}/renew", "ssl_renew_certificate"),
    ("GET", "/api/ssl/providers", "ssl_providers"),
    ("POST", "/api/ssl/test", "ssl_test_config"),
    
    // Webhooks
    ("GET", "/api/webhooks", "webhooks_list"),
    ("POST", "/api/webhooks", "webhooks_create"),
    ("GET", "/api/webhooks/{id}", "webhooks_get"),
    ("PUT", "/api/webhooks/{id}", "webhooks_update"),
    ("DELETE", "/api/webhooks/{id}", "webhooks_delete"),
    ("POST", "/api/webhooks/{id}/test", "webhooks_test"),
    ("GET", "/api/webhooks/{id}/deliveries", "webhooks_deliveries"),
    
    // API Keys
    ("GET", "/api/keys", "api_keys_list"),
    ("POST", "/api/keys", "api_keys_create"),
    ("GET", "/api/keys/{id}", "api_keys_get"),
    ("PUT", "/api/keys/{id}", "api_keys_update"),
    ("DELETE", "/api/keys/{id}", "api_keys_delete"),
    ("POST", "/api/keys/{id}/regenerate", "api_keys_regenerate"),
    
    // Integrations
    ("GET", "/api/integrations", "integrations_list"),
    ("GET", "/api/integrations/{name}", "integrations_get"),
    ("PUT", "/api/integrations/{name}", "integrations_configure"),
    ("POST", "/api/integrations/{name}/test", "integrations_test"),
    ("POST", "/api/integrations/{name}/enable", "integrations_enable"),
    ("POST", "/api/integrations/{name}/disable", "integrations_disable"),
];

// WebSocket API
const WEBSOCKET_EVENTS: &[(&str, &str)] = &[
    // Workspace events
    ("workspace.created", "Workspace was created"),
    ("workspace.deleted", "Workspace was deleted"),
    ("workspace.started", "Workspace container started"),
    ("workspace.stopped", "Workspace container stopped"),
    ("workspace.status_changed", "Workspace status changed"),
    
    // File events
    ("file.created", "File was created"),
    ("file.modified", "File was modified"),
    ("file.deleted", "File was deleted"),
    ("file.renamed", "File was renamed"),
    ("file.moved", "File was moved"),
    
    // Terminal events
    ("terminal.created", "Terminal session created"),
    ("terminal.output", "Terminal output received"),
    ("terminal.input", "Terminal input sent"),
    ("terminal.closed", "Terminal session closed"),
    ("terminal.resized", "Terminal was resized"),
    
    // Language server events
    ("lsp.started", "Language server started"),
    ("lsp.stopped", "Language server stopped"),
    ("lsp.diagnostics", "Diagnostics received"),
    ("lsp.completion", "Completion response"),
    ("lsp.hover", "Hover information"),
    ("lsp.goto_definition", "Go to definition response"),
    ("lsp.find_references", "Find references response"),
    ("lsp.document_symbols", "Document symbols response"),
    ("lsp.workspace_symbols", "Workspace symbols response"),
    ("lsp.code_action", "Code action response"),
    ("lsp.formatting", "Document formatting response"),
    ("lsp.rename", "Symbol rename response"),
    
    // Git events
    ("git.status_changed", "Git status changed"),
    ("git.branch_changed", "Current branch changed"),
    ("git.commit_created", "New commit created"),
    ("git.push_completed", "Git push completed"),
    ("git.pull_completed", "Git pull completed"),
    ("git.merge_completed", "Git merge completed"),
    ("git.conflict_detected", "Merge conflict detected"),
    
    // Build and task events
    ("task.started", "Task execution started"),
    ("task.completed", "Task execution completed"),
    ("task.failed", "Task execution failed"),
    ("task.output", "Task output received"),
    ("build.started", "Build process started"),
    ("build.completed", "Build process completed"),
    ("build.failed", "Build process failed"),
    
    // Debug events
    ("debug.session_started", "Debug session started"),
    ("debug.session_stopped", "Debug session stopped"),
    ("debug.breakpoint_hit", "Breakpoint hit"),
    ("debug.step_completed", "Debug step completed"),
    ("debug.variable_changed", "Variable value changed"),
    ("debug.output", "Debug output received"),
    
    // System events
    ("system.resource_warning", "System resource warning"),
    ("system.resource_critical", "System resource critical"),
    ("system.backup_completed", "System backup completed"),
    ("system.update_available", "System update available"),
    ("system.maintenance_mode", "Maintenance mode activated"),
    
    // User events
    ("user.connected", "User connected to workspace"),
    ("user.disconnected", "User disconnected from workspace"),
    ("user.activity", "User activity detected"),
    ("user.idle", "User became idle"),
    
    // Collaboration events
    ("collab.user_joined", "User joined collaboration"),
    ("collab.user_left", "User left collaboration"),
    ("collab.cursor_moved", "Cursor position changed"),
    ("collab.selection_changed", "Selection changed"),
    ("collab.edit_made", "Collaborative edit made"),
    
    // Live server events
    ("live_server.started", "Live server started"),
    ("live_server.stopped", "Live server stopped"),
    ("live_server.reload", "Live reload triggered"),
    ("live_server.error", "Live server error"),
    
    // Container events
    ("container.created", "Container created"),
    ("container.started", "Container started"),
    ("container.stopped", "Container stopped"),
    ("container.resource_limit", "Container hit resource limit"),
    ("container.health_check", "Container health check result"),
    
    // Image events
    ("image.pull_started", "Image pull started"),
    ("image.pull_completed", "Image pull completed"),
    ("image.build_started", "Image build started"),
    ("image.build_completed", "Image build completed"),
    ("image.deleted", "Image deleted"),
    
    // Notification events
    ("notification.created", "Notification created"),
    ("notification.read", "Notification read"),
    ("notification.deleted", "Notification deleted"),
    
    // Alert events
    ("alert.triggered", "Alert triggered"),
    ("alert.resolved", "Alert resolved"),
    ("alert.acknowledged", "Alert acknowledged"),
    
    // Search events
    ("search.started", "Search started"),
    ("search.completed", "Search completed"),
    ("search.results", "Search results received"),
    
    // Configuration events
    ("config.changed", "Configuration changed"),
    ("config.validated", "Configuration validated"),
    ("config.reset", "Configuration reset"),
    
    // Security events
    ("security.login_attempt", "Login attempt"),
    ("security.login_success", "Successful login"),
    ("security.login_failure", "Failed login"),
    ("security.logout", "User logout"),
    ("security.permission_denied", "Permission denied"),
    ("security.suspicious_activity", "Suspicious activity detected"),
    
    // Performance events
    ("performance.slow_operation", "Slow operation detected"),
    ("performance.high_cpu", "High CPU usage"),
    ("performance.high_memory", "High memory usage"),
    ("performance.disk_space_low", "Low disk space"),
    
    // Integration events
    ("integration.connected", "Integration connected"),
    ("integration.disconnected", "Integration disconnected"),
    ("integration.sync_started", "Integration sync started"),
    ("integration.sync_completed", "Integration sync completed"),
    ("integration.error", "Integration error"),
];

struct WebSocketServer {
    connections: HashMap<String, WebSocketConnection>,
    message_router: MessageRouter,
    event_dispatcher: EventDispatcher,
    subscription_manager: SubscriptionManager,
}

struct WebSocketConnection {
    id: String,
    user_id: String,
    workspace_id: Option<String>,
    connection_time: chrono::DateTime<chrono::Utc>,
    last_activity: chrono::DateTime<chrono::Utc>,
    subscriptions: HashSet<String>,
    sender: tokio::sync::mpsc::UnboundedSender<WebSocketMessage>,
}

struct MessageRouter {
    routes: HashMap<String, Box<dyn MessageHandler>>,
    middleware: Vec<Box<dyn MessageMiddleware>>,
}

trait MessageHandler {
    fn handle(&self, message: &WebSocketMessage, connection: &WebSocketConnection) -> Result<Option<WebSocketMessage>>;
}

trait MessageMiddleware {
    fn process(&self, message: &mut WebSocketMessage, connection: &WebSocketConnection) -> Result<bool>;
}

struct EventDispatcher {
    event_handlers: HashMap<String, Vec<Box<dyn EventHandler>>>,
    event_queue: tokio::sync::mpsc::UnboundedReceiver<Event>,
}

trait EventHandler {
    fn handle_event(&self, event: &Event) -> Result<()>;
}

struct Event {
    event_type: String,
    workspace_id: Option<String>,
    user_id: Option<String>,
    data: serde_json::Value,
    timestamp: chrono::DateTime<chrono::Utc>,
}

struct SubscriptionManager {
    subscriptions: HashMap<String, HashSet<String>>, // event_type -> connection_ids
    connection_subscriptions: HashMap<String, HashSet<String>>, // connection_id -> event_types
}

#[derive(Serialize, Deserialize)]
struct WebSocketMessage {
    id: Option<String>,
    message_type: String,
    event_type: Option<String>,
    workspace_id: Option<String>,
    data: serde_json::Value,
    timestamp: chrono::DateTime<chrono::Utc>,
}

struct StaticHandler {
    asset_cache: HashMap<String, CachedAsset>,
    compression_cache: HashMap<String, CompressedAsset>,
    etag_cache: HashMap<String, String>,
}

struct CachedAsset {
    content: Vec<u8>,
    content_type: String,
    last_modified: chrono::DateTime<chrono::Utc>,
    etag: String,
}

struct CompressedAsset {
    gzip_content: Vec<u8>,
    brotli_content: Vec<u8>,
    original_size: usize,
    compressed_size: usize,
}
```

### 7.2 Middleware Stack
```rust
struct MiddlewareStack {
    middleware: Vec<Box<dyn Middleware>>,
}

trait Middleware {
    fn process_request(&self, request: &mut HttpRequest) -> Result<()>;
    fn process_response(&self, response: &mut HttpResponse) -> Result<()>;
}

// Security middleware
struct SecurityMiddleware {
    csrf_protection: CsrfProtection,
    rate_limiter: RateLimiter,
    cors_handler: CorsHandler,
    security_headers: SecurityHeadersMiddleware,
}

struct CsrfProtection {
    token_store: HashMap<String, CsrfToken>,
    token_lifetime: Duration,
    exempt_paths: HashSet<String>,
}

struct CsrfToken {
    token: String,
    created_at: chrono::DateTime<chrono::Utc>,
    user_id: String,
}

struct RateLimiter {
    limiters: HashMap<String, TokenBucket>,
    global_limiter: TokenBucket,
    exempted_ips: HashSet<std::net::IpAddr>,
}

struct TokenBucket {
    capacity: u32,
    tokens: u32,
    refill_rate: u32,
    last_refill: chrono::DateTime<chrono::Utc>,
}

struct CorsHandler {
    allowed_origins: Vec<String>,
    allowed_methods: Vec<String>,
    allowed_headers: Vec<String>,
    max_age: Duration,
    allow_credentials: bool,
}

struct SecurityHeadersMiddleware {
    headers: HashMap<String, String>,
    csp_policy: ContentSecurityPolicy,
}

// Authentication middleware
struct AuthenticationMiddleware {
    auth_manager: AuthenticationManager,
    session_manager: SessionManager,
    api_key_manager: ApiKeyManager,
}

struct AuthenticationManager {
    providers: HashMap<String, Box<dyn AuthProvider>>,
    default_provider: String,
    multi_factor_auth: Option<MfaManager>,
}

trait AuthProvider {
    fn authenticate(&self, credentials: &Credentials) -> Result<User>;
    fn validate_token(&self, token: &str) -> Result<User>;
    fn refresh_token(&self, refresh_token: &str) -> Result<TokenPair>;
}

struct Credentials {
    username: String,
    password: String,
    additional_factors: HashMap<String, String>,
}

struct TokenPair {
    access_token: String,
    refresh_token: String,
    expires_in: Duration,
}

struct SessionManager {
    sessions: HashMap<String, Session>,
    session_store: Box<dyn SessionStore>,
    cleanup_interval: Duration,
}

struct Session {
    id: String,
    user_id: String,
    created_at: chrono::DateTime<chrono::Utc>,
    last_activity: chrono::DateTime<chrono::Utc>,
    ip_address: std::net::IpAddr,
    user_agent: String,
    data: HashMap<String, serde_json::Value>,
}

trait SessionStore {
    fn get(&self, session_id: &str) -> Result<Option<Session>>;
    fn set(&self, session: &Session) -> Result<()>;
    fn delete(&self, session_id: &str) -> Result<()>;
    fn cleanup_expired(&self) -> Result<usize>;
}

struct ApiKeyManager {
    keys: HashMap<String, ApiKey>,
    key_store: Box<dyn ApiKeyStore>,
}

struct ApiKey {
    id: String,
    key_hash: String,
    user_id: String,
    name: String,
    permissions: Vec<String>,
    created_at: chrono::DateTime<chrono::Utc>,
    last_used: Option<chrono::DateTime<chrono::Utc>>,
    expires_at: Option<chrono::DateTime<chrono::Utc>>,
    rate_limit: Option<u32>,
}

trait ApiKeyStore {
    fn get_by_hash(&self, key_hash: &str) -> Result<Option<ApiKey>>;
    fn create(&self, api_key: &ApiKey) -> Result<()>;
    fn update_last_used(&self, key_id: &str) -> Result<()>;
    fn delete(&self, key_id: &str) -> Result<()>;
}

// Logging middleware
struct LoggingMiddleware {
    logger: Box<dyn Logger>,
    format: LogFormat,
    filters: Vec<LogFilter>,
}

enum LogFormat {
    Combined,
    Common,
    Json,
    Custom(String),
}

trait Logger {
    fn log(&self, entry: &LogEntry);
}

struct LogEntry {
    timestamp: chrono::DateTime<chrono::Utc>,
    method: String,
    path: String,
    status_code: u16,
    response_time: Duration,
    user_agent: Option<String>,
    ip_address: std::net::IpAddr,
    user_id: Option<String>,
    request_id: String,
    request_size: usize,
    response_size: usize,
}

// Compression middleware
struct CompressionMiddleware {
    algorithms: Vec<CompressionAlgorithm>,
    min_size: usize,
    excluded_types: HashSet<String>,
}

enum CompressionAlgorithm {
    Gzip,
    Brotli,
    Deflate,
}

// Error handling middleware
struct ErrorHandlingMiddleware {
    error_handlers: HashMap<u16, Box<dyn ErrorHandler>>,
    default_handler: Box<dyn ErrorHandler>,
    debug_mode: bool,
}

trait ErrorHandler {
    fn handle(&self, error: &Error, request: &HttpRequest) -> HttpResponse;
}

// Metrics middleware
struct MetricsMiddleware {
    metrics_collector: MetricsCollector,
    enabled_metrics: HashSet<MetricType>,
}

struct MetricsCollector {
    request_counter: Counter,
    response_time_histogram: Histogram,
    error_counter: Counter,
    active_connections_gauge: Gauge,
}

// Cache middleware
struct CacheMiddleware {
    cache_store: Box<dyn CacheStore>,
    cache_policies: HashMap<String, CachePolicy>,
    default_policy: CachePolicy,
}

struct CachePolicy {
    ttl: Duration,
    max_size: Option<usize>,
    cache_key_generator: Box<dyn Fn(&HttpRequest) -> String>,
    should_cache: Box<dyn Fn(&HttpRequest, &HttpResponse) -> bool>,
}

trait CacheStore {
    fn get(&self, key: &str) -> Result<Option<CachedResponse>>;
    fn set(&self, key: &str, response: &CachedResponse, ttl: Duration) -> Result<()>;
    fn delete(&self, key: &str) -> Result<()>;
    fn clear(&self) -> Result<()>;
}

struct CachedResponse {
    status_code: u16,
    headers: HashMap<String, String>,
    body: Vec<u8>,
    cached_at: chrono::DateTime<chrono::Utc>,
}
```

---

## 8. First User Experience

### 8.1 Binary Installation & Startup
```rust
struct FirstUserExperience {
    binary_behavior: BinaryBehavior,
    system_setup: SystemSetup,
    web_setup_flow: WebSetupFlow,
    onboarding: OnboardingFlow,
}

struct BinaryBehavior {
    silent_startup: bool,          // true
    auto_setup: bool,              // true
    interactive_mode: bool,        // false
    daemon_mode: bool,             // true
}

impl BinaryBehavior {
    fn run_startup_sequence() -> Result<()> {
        // 1. Silent system checks and setup
        SystemSetup::check_prerequisites()?;
        SystemSetup::create_system_user_if_needed()?;
        SystemSetup::setup_directories()?;
        SystemSetup::initialize_database()?;
        
        // 2. Start services silently
        let server = WebServer::start_silent()?;
        
        // 3. Single output line
        println!("🌐 Caspod ready at http://localhost:8080");
        
        // 4. Run in background
        server.run().await
    }
}

struct SystemSetup {
    privilege_manager: PrivilegeManager,
    directory_manager: DirectoryManager,
    database_initializer: DatabaseInitializer,
    service_manager: ServiceManager,
}

impl SystemSetup {
    fn check_prerequisites() -> Result<()> {
        // Check OS compatibility
        let os_info = os_info::get();
        if !Self::is_supported_os(&os_info) {
            return Err(Error::UnsupportedOS(os_info.os_type()));
        }
        
        // Check architecture
        let arch = std::env::consts::ARCH;
        if !["x86_64", "aarch64"].contains(&arch) {
            return Err(Error::UnsupportedArchitecture(arch.to_string()));
        }
        
        // Check disk space
        let free_space = Self::get_free_disk_space()?;
        if free_space < 1_000_000_000 { // 1GB minimum
            return Err(Error::InsufficientDiskSpace(free_space));
        }
        
        // Check memory
        let total_memory = Self::get_total_memory()?;
        if total_memory < 2_000_000_000 { // 2GB minimum
            return Err(Error::InsufficientMemory(total_memory));
        }
        
        Ok(())
    }
    
    fn create_system_user_if_needed() -> Result<()> {
        // Only if running as root
        if !nix::unistd::getuid().is_root() {
            return Ok(()); // Already running as non-root
        }
        
        // Check if caspod user already exists
        if Self::user_exists("caspod")? {
            return Ok(());
        }
        
        // Create system user with random UID in system range
        let uid = Self::find_free_system_uid()?;
        Self::create_user("caspod", uid)?;
        
        Ok(())
    }
    
    fn setup_directories() -> Result<()> {
        let paths = if nix::unistd::getuid().is_root() {
            DirectoryPaths::system()
        } else {
            DirectoryPaths::user()
        };
        
        // Create all necessary directories
        for path in paths.all_paths() {
            std::fs::create_dir_all(&path)?;
            
            // Set appropriate permissions
            if nix::unistd::getuid().is_root() {
                Self::set_ownership(&path, "caspod", "caspod")?;
                Self::set_permissions(&path, 0o755)?;
            }
        }
        
        Ok(())
    }
    
    fn initialize_database() -> Result<()> {
        let db_path = DirectoryPaths::current().database_dir.join("caspod.db");
        
        if !db_path.exists() {
            DatabaseInitializer::create_database(&db_path)?;
            DatabaseInitializer::run_migrations(&db_path)?;
            DatabaseInitializer::insert_default_data(&db_path)?;
        }
        
        Ok(())
    }
    
    fn find_free_system_uid() -> Result<u32> {
        // Find free UID in system range (100-998)
        for uid in 100..999 {
            if !Self::uid_exists(uid)? {
                return Ok(uid);
            }
        }
        Err(Error::NoFreeSystemUID)
    }
    
    fn create_user(username: &str, uid: u32) -> Result<()> {
        let output = std::process::Command::new("useradd")
            .args(&[
                "--system",                    // System user
                "--uid", &uid.to_string(),     // Specific UID
                "--gid", &uid.to_string(),     // Same GID
                "--create-home",               // Create home directory
                "--home-dir", &format!("/var/lib/{}", username), // Custom home
                "--shell", "/bin/false",       // No shell access
                "--comment", "Caspod System User",
                username,
            ])
            .output()?;
        
        if !output.status.success() {
            return Err(Error::UserCreationFailed(
                String::from_utf8_lossy(&output.stderr).to_string()
            ));
        }
        
        Ok(())
    }
    
    fn drop_privileges() -> Result<()> {
        if !nix::unistd::getuid().is_root() {
            return Ok(()); // Already running as non-root
        }
        
        // Get caspod user info
        let user = nix::unistd::User::from_name("caspod")?
            .ok_or(Error::UserNotFound("caspod".to_string()))?;
        
        // Drop privileges permanently
        nix::unistd::setgid(user.gid)?;
        nix::unistd::setuid(user.uid)?;
        
        // Verify we can't regain root
        if nix::unistd::seteuid(nix::unistd::Uid::from_raw(0)).is_ok() {
            return Err(Error::PrivilegeDropFailed);
        }
        
        Ok(())
    }
}

struct DirectoryPaths {
    bin_dir: PathBuf,
    config_dir: PathBuf,
    data_dir: PathBuf,
    log_dir: PathBuf,
    runtime_dir: PathBuf,
    cache_dir: PathBuf,
    
    // Subdirectories
    database_dir: PathBuf,
    workspaces_dir: PathBuf,
    containers_dir: PathBuf,
    images_dir: PathBuf,
    templates_dir: PathBuf,
    backups_dir: PathBuf,
}

impl DirectoryPaths {
    fn system() -> Self {
        let data_dir = PathBuf::from("/var/lib/caspod");
        
        Self {
            bin_dir: PathBuf::from("/usr/local/bin"),
            config_dir: PathBuf::from("/etc/caspod"),
            data_dir: data_dir.clone(),
            log_dir: PathBuf::from("/var/log/caspod"),
            runtime_dir: PathBuf::from("/run/caspod"),
            cache_dir: PathBuf::from("/var/cache/caspod"),
            
            database_dir: data_dir.join("database"),
            workspaces_dir: data_dir.join("workspaces"),
            containers_dir: data_dir.join("containers"),
            images_dir: data_dir.join("images"),
            templates_dir: data_dir.join("templates"),
            backups_dir: data_dir.join("backups"),
        }
    }
    
    fn user() -> Self {
        let home_dir = dirs::home_dir().unwrap_or_else(|| PathBuf::from("."));
        let data_dir = home_dir.join(".local/share/caspod");
        
        Self {
            bin_dir: home_dir.join(".local/bin"),
            config_dir: home_dir.join(".config/caspod"),
            data_dir: data_dir.clone(),
            log_dir: data_dir.join("logs"),
            runtime_dir: PathBuf::from(&format!("/tmp/caspod-{}", nix::unistd::getuid())),
            cache_dir: home_dir.join(".cache/caspod"),
            
            database_dir: data_dir.join("database"),
            workspaces_dir: data_dir.join("workspaces"),
            containers_dir: data_dir.join("containers"),
            images_dir: data_dir.join("images"),
            templates_dir: data_dir.join("templates"),
            backups_dir: data_dir.join("backups"),
        }
    }
    
    fn current() -> Self {
        if nix::unistd::getuid().is_root() {
            Self::system()
        } else {
            Self::user()
        }
    }
    
    fn all_paths(&self) -> Vec<&PathBuf> {
        vec![
            &self.bin_dir,
            &self.config_dir,
            &self.data_dir,
            &self.log_dir,
            &self.runtime_dir,
            &self.cache_dir,
            &self.database_dir,
            &self.workspaces_dir,
            &self.containers_dir,
            &self.images_dir,
            &self.templates_dir,
            &self.backups_dir,
        ]
    }
}
```

### 8.2 Web Setup Flow
```rust
struct WebSetupFlow {
    setup_steps: Vec<SetupStep>,
    setup_state: SetupState,
    validation_rules: ValidationRules,
}

#[derive(Serialize, Deserialize)]
struct SetupState {
    current_step: usize,
    completed_steps: HashSet<usize>,
    data: HashMap<String, serde_json::Value>,
    admin_created: bool,
    setup_completed: bool,
}

struct SetupStep {
    id: String,
    title: String,
    description: String,
    component: String,
    required: bool,
    validator: Box<dyn Fn(&serde_json::Value) -> Result<()>>,
    prerequisites: Vec<String>,
}

const SETUP_STEPS: &[(&str, &str, &str, &str, bool)] = &[
    (
        "admin_account",
        "Create Administrator Account",
        "Create the first administrator account to manage your Caspod instance",
        "AdminAccountSetup",
        true
    ),
    (
        "instance_settings",
        "Instance Configuration",
        "Configure basic settings for your Caspod instance",
        "InstanceSettings",
        true
    ),
    (
        "network_security",
        "Network & Security",
        "Configure network settings, SSL/TLS, and security options",
        "NetworkSecurity",
        true
    ),
    (
        "resource_limits",
        "Resource Limits",
        "Set resource limits and performance parameters",
        "ResourceLimits",
        true
    ),
    (
        "container_images",
        "Default Container Images",
        "Configure default container images and registry settings",
        "ContainerImages",
        false
    ),
    (
        "authentication",
        "Authentication Method",
        "Choose authentication methods and configure external providers",
        "AuthenticationSetup",
        false
    ),
    (
        "backup_monitoring",
        "Backup & Monitoring",
        "Configure backup settings and monitoring preferences",
        "BackupMonitoring",
        false
    ),
];

impl WebSetupFlow {
    fn new() -> Self {
        let setup_steps = SETUP_STEPS.iter().map(|(id, title, description, component, required)| {
            SetupStep {
                id: id.to_string(),
                title: title.to_string(),
                description: description.to_string(),
                component: component.to_string(),
                required: *required,
                validator: Self::create_validator(id),
                prerequisites: Self::get_prerequisites(id),
            }
        }).collect();
        
        Self {
            setup_steps,
            setup_state: SetupState::default(),
            validation_rules: ValidationRules::new(),
        }
    }
    
    fn handle_initial_access() -> Result<SetupResponse> {
        // Check if setup is already completed
        if Self::is_setup_completed()? {
            return Ok(SetupResponse::Redirect("/dashboard".to_string()));
        }
        
        // Check if admin user exists
        if Self::admin_user_exists()? {
            return Ok(SetupResponse::Redirect("/login".to_string()));
        }
        
        // Start setup flow
        Ok(SetupResponse::Setup(SetupData {
            steps: Self::get_setup_steps(),
            current_step: 0,
            progress: 0.0,
            data: HashMap::new(),
        }))
    }
    
    fn create_validator(step_id: &str) -> Box<dyn Fn(&serde_json::Value) -> Result<()>> {
        match step_id {
            "admin_account" => Box::new(|data| {
                let username = data.get("username")
                    .and_then(|v| v.as_str())
                    .ok_or(Error::MissingField("username"))?;
                
                let password = data.get("password")
                    .and_then(|v| v.as_str())
                    .ok_or(Error::MissingField("password"))?;
                
                let email = data.get("email")
                    .and_then(|v| v.as_str())
                    .ok_or(Error::MissingField("email"))?;
                
                // Validate username
                if username.len() < 3 || username.len() > 32 {
                    return Err(Error::InvalidUsername("Username must be 3-32 characters".to_string()));
                }
                
                if !username.chars().all(|c| c.is_alphanumeric() || c == '_' || c == '-') {
                    return Err(Error::InvalidUsername("Username can only contain letters, numbers, underscore, and dash".to_string()));
                }
                
                // Must be "administrator" for first admin
                if username != "administrator" {
                    return Err(Error::InvalidUsername("First admin username must be 'administrator'".to_string()));
                }
                
                // Validate password
                if password.len() < 12 {
                    return Err(Error::WeakPassword("Password must be at least 12 characters".to_string()));
                }
                
                let has_upper = password.chars().any(|c| c.is_uppercase());
                let has_lower = password.chars().any(|c| c.is_lowercase());
                let has_digit = password.chars().any(|c| c.is_numeric());
                let has_symbol = password.chars().any(|c| !c.is_alphanumeric());
                
                if !has_upper || !has_lower || !has_digit || !has_symbol {
                    return Err(Error::WeakPassword("Password must contain uppercase, lowercase, digit, and symbol".to_string()));
                }
                
                // Validate email
                if !email.contains('@') || !email.contains('.') {
                    return Err(Error::InvalidEmail("Invalid email format".to_string()));
                }
                
                Ok(())
            }),
            
            "instance_settings" => Box::new(|data| {
                let instance_name = data.get("instance_name")
                    .and_then(|v| v.as_str())
                    .ok_or(Error::MissingField("instance_name"))?;
                
                if instance_name.is_empty() || instance_name.len() > 64 {
                    return Err(Error::InvalidInstanceName("Instance name must be 1-64 characters".to_string()));
                }
                
                let organization = data.get("organization")
                    .and_then(|v| v.as_str())
                    .unwrap_or("casapps");
                
                if organization.len() > 64 {
                    return Err(Error::InvalidOrganization("Organization name must be max 64 characters".to_string()));
                }
                
                Ok(())
            }),
            
            "network_security" => Box::new(|data| {
                let host = data.get("host")
                    .and_then(|v| v.as_str())
                    .unwrap_or("0.0.0.0");
                
                let port = data.get("port")
                    .and_then(|v| v.as_u64())
                    .unwrap_or(8080) as u16;
                
                // Validate host
                if host != "0.0.0.0" && host != "127.0.0.1" && !host.parse::<std::net::IpAddr>().is_ok() {
                    return Err(Error::InvalidHost("Invalid host address".to_string()));
                }
                
                // Validate port
                if port < 1024 && nix::unistd::getuid().is_root() == false {
                    return Err(Error::InvalidPort("Non-root users cannot bind to ports below 1024".to_string()));
                }
                
                if port == 22 || port == 80 || port == 443 {
                    return Err(Error::InvalidPort("Cannot use reserved system ports".to_string()));
                }
                
                // Validate SSL settings if enabled
                if let Some(ssl_enabled) = data.get("ssl_enabled").and_then(|v| v.as_bool()) {
                    if ssl_enabled {
                        let cert_source = data.get("cert_source")
                            .and_then(|v| v.as_str())
                            .ok_or(Error::MissingField("cert_source"))?;
                        
                        match cert_source {
                            "letsencrypt" => {
                                let domain = data.get("domain")
                                    .and_then(|v| v.as_str())
                                    .ok_or(Error::MissingField("domain"))?;
                                
                                let email = data.get("email")
                                    .and_then(|v| v.as_str())
                                    .ok_or(Error::MissingField("email"))?;
                                
                                if domain.is_empty() || !domain.contains('.') {
                                    return Err(Error::InvalidDomain("Invalid domain name".to_string()));
                                }
                                
                                if !email.contains('@') {
                                    return Err(Error::InvalidEmail("Invalid email for Let's Encrypt".to_string()));
                                }
                            },
                            "custom" => {
                                let cert_path = data.get("cert_path")
                                    .and_then(|v| v.as_str())
                                    .ok_or(Error::MissingField("cert_path"))?;
                                
                                let key_path = data.get("key_path")
                                    .and_then(|v| v.as_str())
                                    .ok_or(Error::MissingField("key_path"))?;
                                
                                if !std::path::Path::new(cert_path).exists() {
                                    return Err(Error::FileNotFound(format!("Certificate file not found: {}", cert_path)));
                                }
                                
                                if !std::path::Path::new(key_path).exists() {
                                    return Err(Error::FileNotFound(format!("Private key file not found: {}", key_path)));
                                }
                            },
                            "self_signed" => {
                                // No additional validation needed
                            },
                            _ => {
                                return Err(Error::InvalidCertSource("Invalid certificate source".to_string()));
                            }
                        }
                    }
                }
                
                Ok(())
            }),
            
            "resource_limits" => Box::new(|data| {
                let max_workspaces = data.get("max_workspaces")
                    .and_then(|v| v.as_u64())
                    .unwrap_or(10) as u32;
                
                let max_cpu_per_workspace = data.get("max_cpu_per_workspace")
                    .and_then(|v| v.as_f64())
                    .unwrap_or(2.0);
                
                let max_memory_per_workspace = data.get("max_memory_per_workspace")
                    .and_then(|v| v.as_str())
                    .unwrap_or("4g");
                
                // Validate limits
                if max_workspaces == 0 || max_workspaces > 1000 {
                    return Err(Error::InvalidResourceLimit("Max workspaces must be 1-1000".to_string()));
                }
                
                if max_cpu_per_workspace <= 0.0 || max_cpu_per_workspace > 32.0 {
                    return Err(Error::InvalidResourceLimit("Max CPU per workspace must be 0.1-32.0".to_string()));
                }
                
                // Parse memory limit
                let memory_bytes = Self::parse_memory_string(max_memory_per_workspace)?;
                if memory_bytes < 1_000_000_000 || memory_bytes > 64_000_000_000 { // 1GB - 64GB
                    return Err(Error::InvalidResourceLimit("Max memory per workspace must be 1g-64g".to_string()));
                }
                
                Ok(())
            }),
            
            _ => Box::new(|_| Ok(())) // Default validator
        }
    }
    
    fn get_prerequisites(step_id: &str) -> Vec<String> {
        match step_id {
            "admin_account" => vec![],
            "instance_settings" => vec!["admin_account".to_string()],
            "network_security" => vec!["admin_account".to_string(), "instance_settings".to_string()],
            "resource_limits" => vec!["admin_account".to_string()],
            "container_images" => vec!["admin_account".to_string()],
            "authentication" => vec!["admin_account".to_string()],
            "backup_monitoring" => vec!["admin_account".to_string()],
            _ => vec![]
        }
    }
    
    fn parse_memory_string(memory_str: &str) -> Result<u64> {
        let memory_str = memory_str.trim().to_lowercase();
        
        if let Some(value_str) = memory_str.strip_suffix("g") {
            let value: f64 = value_str.parse()?;
            Ok((value * 1_000_000_000.0) as u64)
        } else if let Some(value_str) = memory_str.strip_suffix("m") {
            let value: f64 = value_str.parse()?;
            Ok((value * 1_000_000.0) as u64)
        } else if let Some(value_str) = memory_str.strip_suffix("k") {
            let value: f64 = value_str.parse()?;
            Ok((value * 1_000.0) as u64)
        } else {
            let value: u64 = memory_str.parse()?;
            Ok(value)
        }
    }
}

#[derive(Serialize)]
enum SetupResponse {
    Setup(SetupData),
    Redirect(String),
    Complete(CompletionData),
}

#[derive(Serialize)]
struct SetupData {
    steps: Vec<SetupStepInfo>,
    current_step: usize,
    progress: f64,
    data: HashMap<String, serde_json::Value>,
}

#[derive(Serialize)]
struct SetupStepInfo {
    id: String,
    title: String,
    description: String,
    component: String,
    required: bool,
    completed: bool,
}

#[derive(Serialize)]
struct CompletionData {
    admin_token: String,          // Show only once
    instance_url: String,
    setup_summary: SetupSummary,
    next_steps: Vec<String>,
}

#[derive(Serialize)]
struct SetupSummary {
    instance_name: String,
    admin_username: String,
    host: String,
    port: u16,
    ssl_enabled: bool,
    max_workspaces: u32,
    authentication_methods: Vec<String>,
    backup_enabled: bool,
}
```

### 8.3 Token Display & Security
```rust
struct TokenManager {
    tokens: HashMap<String, AdminToken>,
    display_policy: TokenDisplayPolicy,
}

struct AdminToken {
    token: String,
    user_id: String,
    created_at: chrono::DateTime<chrono::Utc>,
    expires_at: Option<chrono::DateTime<chrono::Utc>>,
    displayed: bool,              // Can only be displayed once
    permissions: Vec<String>,
}

struct TokenDisplayPolicy {
    show_only_once: bool,         // true
    require_copy_confirmation: bool, // true
    auto_hide_after: Duration,    // 30 seconds
    warn_before_hide: Duration,   // 10 seconds
}

impl TokenManager {
    fn create_admin_token(user_id: &str) -> Result<(String, AdminToken)> {
        let token = Self::generate_secure_token(64)?;
        let admin_token = AdminToken {
            token: token.clone(),
            user_id: user_id.to_string(),
            created_at: chrono::Utc::now(),
            expires_at: None, // Admin tokens don't expire by default
            displayed: false,
            permissions: vec!["admin:*".to_string()],
        };
        
        Ok((token, admin_token))
    }
    
    fn display_token(&mut self, token_id: &str) -> Result<TokenDisplayResponse> {
        let token = self.tokens.get_mut(token_id)
            .ok_or(Error::TokenNotFound)?;
        
        if token.displayed {
            return Ok(TokenDisplayResponse::AlreadyDisplayed);
        }
        
        token.displayed = true;
        
        Ok(TokenDisplayResponse::Token {
            token: token.token.clone(),
            warning: "⚠️ This token will only be shown once. Please copy it now!".to_string(),
            auto_hide_in: 30, // seconds
        })
    }
    
    fn generate_secure_token(length: usize) -> Result<String> {
        use rand::Rng;
        const CHARSET: &[u8] = b"ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789";
        
        let mut rng = rand::thread_rng();
        let token: String = (0..length)
            .map(|_| {
                let idx = rng.gen_range(0..CHARSET.len());
                CHARSET[idx] as char
            })
            .collect();
        
        Ok(token)
    }
}

#[derive(Serialize)]
enum TokenDisplayResponse {
    Token {
        token: String,
        warning: String,
        auto_hide_in: u32,
    },
    AlreadyDisplayed,
}
```

---

This comprehensive specification defines every aspect of the Caspod IDE system, from the embedded container runtime and scheduler to the complete web interface and configuration system. The specification includes:

**Core Systems:**
- Embedded OCI container runtime with full lifecycle management
- Advanced scheduler with ML-based resource optimization  
- Complete language server integration for 15+ languages
- Built-in 500+ IDE features without plugins
- Responsive web UI supporting mobile to 8K displays
- Template and snippet system with dynamic variables
- Comprehensive security framework
- Complete configuration management with database storage

**Key Technical Highlights:**
- Single static binary with no external dependencies
- Built-in container runtime (no Docker required)
- Complete project auto-detection and configuration
- Support for DevContainer, Gitpod, and custom configs
- SSL/TLS with Let's Encrypt integration
- Advanced monitoring and alerting
- Comprehensive backup and recovery
- Full REST and WebSocket APIs

**Enterprise Features:**
- Multi-user support with RBAC
- LDAP/OAuth2/SAML integration
- Resource quotas and fair share scheduling
- Audit logging and compliance
- High availability and scaling
- Performance monitoring and optimization

The specification provides a complete blueprint for building a production-ready, self-hosted cloud IDE that rivals commercial offerings while maintaining simplicity for end users.

