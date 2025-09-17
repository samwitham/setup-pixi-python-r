## Setting Up Pixi Environment for learning Python and R in WSL (Windows Subsystem for Linux)

## 1. System Prerequisites and Package Installation

First, ensure your WSL environment has the necessary system packages:

```bash
# Update package lists and install essential packages
sudo apt-get update && sudo apt-get upgrade -y

# Install git and development dependencies
sudo apt-get install -y git curl wget build-essential procps apt-utils \
    libgsl-dev locales libicu-dev libcurl4-openssl-dev \
    libnode-dev libxml2-dev make pandoc ca-certificates

# Clean up package cache
sudo apt-get clean && sudo rm -rf /var/lib/apt/lists/*
```

## 2. Install Pixi Package Manager

Install pixi using the official installer, which automatically configures PATH:

```bash
# Install pixi with automatic PATH configuration
curl -fsSL https://pixi.sh/install.sh | bash

# Reload your shell configuration
source ~/.bashrc  # or ~/.zshrc if using zsh
```

**Note:** The installer automatically adds `~/.pixi/bin` to your PATH in your shell configuration file.

## 3. Set Up Enhanced Shell Environment (Optional but Recommended)

### 3.1: Install Oh My Zsh for Better Terminal Experience

```bash
# Install Oh My Zsh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

# Install useful zsh plugins for development
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

### 3.2: Configure Pixi Autocompletion

Add pixi autocompletion to your shell configuration:

**For Zsh (recommended):**

```bash
# Add to ~/.zshrc
echo 'autoload -Uz compinit && compinit' >> ~/.zshrc
echo 'eval "$(pixi completion --shell zsh)"' >> ~/.zshrc
```

**For Bash:**

```bash
# Add to ~/.bashrc
echo 'eval "$(pixi completion --shell bash)"' >> ~/.bashrc
```

## 4. Create and Configure Your Learning Environment

### 4.1: Initialize the Pixi Project

```bash
# Create project directory and initialize pixi environment
mkdir -p ~/pixi_envs/learn_python_r
cd ~/pixi_envs/learn_python_r
pixi init

# Add recommended conda channels for comprehensive package access
pixi project channel add conda-forge
pixi project channel add bioconda
```

### 4.2: Configure Post-Link Scripts (Important for R packages)

```bash
# Enable post-link scripts for proper R package installation
pixi config set --local run-post-link-scripts insecure
```

## 5. Install Core Languages and Packages

### 5.1: Add Python, R, and Essential Packages

Install all necessary packages in one command for better dependency resolution:

```bash
pixi add r-base r-devtools r-biocmanager r-remotes r-roxygen2 r-testthat \
    r-usethis r-argparser r-ggplot2 r-data.table r-svglite r-rcpp \
    r-future r-dplyr r-matrix r-purrr r-box r-openxlsx r-markdown \
    r-irkernel bioconductor-edger python ipykernel numpy matplotlib \
    seaborn pandas biopython jupyter
```

### 5.2: Install Development Tools Globally

Use pixi global for tools you want available system-wide:

```bash
# Install global development tools
pixi global install jupyter_client ruff pre-commit
```

## 6. Configure Jupyter Kernels for Both Languages

### 6.1: Set Up R Kernel for Jupyter

```bash
# Enter the pixi environment and configure R kernel
pixi shell
R -e "IRkernel::installspec()"
exit  # Exit pixi shell
```

### 6.2: Set Up Python Kernel for Jupyter

```bash
# Configure Python kernel within the pixi environment
pixi run python -m ipykernel install --user --name learn-python-r --display-name "Learn Python & R (Pixi)"
```

## 7. Git Configuration

Set up git with your personal information:

```bash
# Configure git with your details (replace with your information)
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

## 8. Create Environment Activation Function

Instead of a custom function, use pixi's built-in shell-hook for better integration.

### 8.1: Add Activation Function to Shell Configuration

To automatically add the environment activation function to the end of your `.zshrc`, run this command in your terminal:

```bash
echo '
# Pixi environment activation function for learn_python_r
learn_python_r() {
    if [ -d ~/pixi_envs/learn_python_r ]; then
        cd ~/pixi_envs/learn_python_r
        eval "$(pixi shell-hook)"
    else
        echo "learn_python_r environment directory not found"
    fi
}
' >> ~/.zshrc
```

Then, reload your shell configuration to activate the function:

```bash
source ~/.zshrc
```

**For Bash users**, use the same approach but append to `~/.bashrc` instead:

```bash
echo '
# Pixi environment activation function for learn_python_r
learn_python_r() {
    if [ -d ~/pixi_envs/learn_python_r ]; then
        cd ~/pixi_envs/learn_python_r
        eval "$(pixi shell-hook)"
    else
        echo "learn_python_r environment directory not found"
    fi
}
' >> ~/.bashrc
source ~/.bashrc
```

### 8.2: Apply Configuration Changes

```bash
# Reload your shell configuration
source ~/.zshrc  # or ~/.bashrc for bash users
```

## 9. Start Your Learning Environment

Now you can easily enter your configured environment:

```bash
# Activate the learning environment
learn_python_r

# Your prompt should change to indicate the active environment
# You can now use python, R, and jupyter with all packages available
```

## 10. Set Up VS Code Integration and Launch Jupyter

### 10.1: Install Required VS Code Extensions

You can install the required extensions using either method below. **Note**: VS Code and its extensions install globally in WSL, so you only need to do this once for all your pixi environments.

#### Option A: Command Line Installation (from WSL terminal)

```bash
# Run these commands from your WSL terminal
# Make sure VS Code is installed and the 'code' command is available
code --install-extension ms-toolsai.jupyter
code --install-extension ms-python.python
code --install-extension GitHub.vscode-pull-request-github@prerelease
code --install-extension charliermarsh.ruff
code --install-extension REditorSupport.r
code --install-extension ms-vscode-remote.remote-wsl
```

#### Option B: Install via VS Code Extensions Marketplace (GUI method)

1. **Open VS Code**
2. **Open Extensions view**: Press `Ctrl+Shift+X` or click the Extensions icon in the Activity Bar
3. **Search and install each extension** by searching for these exact names:
   - **Jupyter** (by Microsoft) - `ms-toolsai.jupyter`
   - **Python** (by Microsoft) - `ms-python.python`  
   - **GitHub Pull Requests and Issues** (by Microsoft) - `GitHub.vscode-pull-request-github@prerelease`
   - **Ruff** (by Charlie Marsh) - `charliermarsh.ruff`
   - **R** (by REditorSupport) - `REditorSupport.r`
   - **WSL** (by Microsoft) - `ms-vscode-remote.remote-wsl`

4. **Click "Install"** for each extension
5. **Reload VS Code** if prompted

### 10.2: Configure Project-Specific VS Code Settings (Optional but Recommended)

While VS Code and extensions are installed globally, you can create project-specific settings to automatically configure the correct Python interpreter and tools for this pixi environment. This is especially useful when working with multiple pixi projects.

**Benefits of project-specific settings:**
- VS Code automatically uses the correct Python interpreter for this project
- Jupyter kernels are properly configured
- No need to manually select the interpreter each time you open the project
- Each pixi environment can have its own configuration

```bash
# Create VS Code configuration directory
mkdir -p ~/pixi_envs/learn_python_r/.vscode

# Create project-specific VS Code settings file
cat > ~/pixi_envs/learn_python_r/.vscode/settings.json << 'EOF'
{
    "python.defaultInterpreterPath": "./.pixi/envs/default/bin/python",
    "jupyter.kernels.filter": [
        {
            "path": "./.pixi/envs/default/bin/python",
            "type": "pythonEnvironment"
        }
    ],
    "ruff.path": ["./.pixi/envs/default/bin/ruff"],
    "python.linting.enabled": true,
    "python.linting.ruffEnabled": true,
    "python.formatting.provider": "none",
    "python.analysis.typeCheckingMode": "basic",
    "files.associations": {
        "*.qmd": "markdown"
    }
}
EOF
```

**Alternative: Manual Configuration**
If you skip the settings file, you can manually configure VS Code each time:
1. Open your project in VS Code
2. Press `Ctrl+Shift+P` → "Python: Select Interpreter" 
3. Choose the interpreter from `.pixi/envs/default/bin/python`

### 10.3: Launch Jupyter from VS Code

Within your activated pixi environment:

```bash
# Navigate to your project directory (if not already there)
cd ~/pixi_envs/learn_python_r

# Activate the pixi environment
learn_python_r

# Open VS Code in the current directory
code .
```

**In VS Code:**

1. **Open Command Palette**: Press `Ctrl+Shift+P` (or `Cmd+Shift+P` on Mac)
2. **Create New Jupyter Notebook**: Type "Jupyter: Create New Notebook" and select it
3. **Select Kernel**: When prompted, choose either:
   - "Learn Python & R (Pixi)" for the Python kernel
   - Your R kernel for R development
4. **Start coding**: You now have access to both Python and R with all installed packages

### 10.4: Alternative: Launch Jupyter Lab from VS Code Terminal

You can also launch Jupyter Lab directly from VS Code's integrated terminal:

1. **Open Terminal in VS Code**: Press `` Ctrl+` `` (backtick)
2. **Ensure pixi environment is active**: Your terminal prompt should show the environment
3. **Launch Jupyter Lab**:
   ```bash
   jupyter lab
   ```
4. **Access through browser**: Jupyter Lab will open in your default browser with full functionality

## Additional Considerations

- **Performance**: Store your projects in the WSL filesystem (`~/`) rather than Windows filesystem (`/mnt/c/`) for better performance
- **VS Code Integration**: The WSL extension in VS Code provides seamless development experience
- **Multiple Environments**: Since VS Code is installed globally, you can use it with multiple pixi environments
- **Alternative Activation**: You can also use `pixi shell` directly from the project directory for quick access
- **Package Updates**: Use `pixi update` to update all packages while maintaining compatibility
- **Kernel Selection**: In VS Code notebooks, you can easily switch between Python and R kernels using the kernel selector

## Troubleshooting

### Common Issues and Solutions

1. **Pixi command not found**: Ensure you've reloaded your shell configuration after installation

2. **R packages not installing**: Make sure post-link scripts are enabled with `pixi config set --local run-post-link-scripts true`

3. **'code' command not found in WSL**: 
   - Install VS Code on Windows first
   - Install the WSL extension in VS Code
   - From VS Code, open Command Palette (`Ctrl+Shift+P`) and run "WSL: Connect to WSL"
   - This should make the `code` command available in WSL

4. **Jupyter kernels not appearing in VS Code**: 
   - Check that you've installed the kernels correctly (step 6)
   - Reload VS Code window (`Ctrl+Shift+P` → "Developer: Reload Window")
   - Verify kernels are installed with `jupyter kernelspec list`

5. **Slow performance**: Ensure your project is stored in the WSL filesystem, not mounted Windows drives

6. **VS Code not detecting Python interpreter**: 
   - If you created the `.vscode/settings.json` file, VS Code should auto-detect
   - Otherwise, use `Ctrl+Shift+P` → "Python: Select Interpreter"
   - Choose the interpreter from `.pixi/envs/default/bin/python`

7. **Extensions not installing via command line**:
   - Try the GUI method (Option B) instead
   - Ensure VS Code is properly connected to WSL
   - Check that you have internet connectivity in WSL

## Next Steps

Once your environment is set up, you can:
- Create new notebooks directly in VS Code using `Ctrl+Shift+P` → "Jupyter: Create New Notebook"
- Install additional packages with `pixi add <package-name>`
- Share your environment configuration by committing the `pixi.toml` and `pixi.lock` files to version control
- Use VS Code's integrated Git features for version control
- Create additional pixi environments and use the same VS Code setup
- Explore the extensive conda-forge and bioconda package ecosystems available through pixi
- Leverage VS Code's debugging capabilities for both Python and R code
