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
pixi config set --local run-post-link-scripts true
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
pixi global install jupyter-client ruff pre-commit dvc
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

**For Zsh users, add to ~/.zshrc:**

```bash
# Pixi environment activation function
learn_python_r() {
    if [ -d ~/pixi_envs/learn_python_r ]; then
        cd ~/pixi_envs/learn_python_r
        eval "$(pixi shell-hook)"
    else
        echo "learn_python_r environment directory not found"
    fi
}
```

**For Bash users, add to ~/.bashrc:**

```bash
# Pixi environment activation function
learn_python_r() {
    if [ -d ~/pixi_envs/learn_python_r ]; then
        cd ~/pixi_envs/learn_python_r
        eval "$(pixi shell-hook)"
    else
        echo "learn_python_r environment directory not found"
    fi
}
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

## 10. Launch Jupyter Lab

Within your activated environment:

```bash
# Start Jupyter Lab
jupyter lab
```

You should now see both Python and R kernels available in Jupyter Lab.

## Additional Considerations

- **Performance**: Store your projects in the WSL filesystem (`~/`) rather than Windows filesystem (`/mnt/c/`) for better performance
- **VS Code Integration**: Install the WSL extension in VS Code for seamless development
- **Alternative Activation**: You can also use `pixi shell` directly from the project directory for quick access
- **Package Updates**: Use `pixi update` to update all packages while maintaining compatibility

## Troubleshooting

### Common Issues and Solutions

1. **Pixi command not found**: Ensure you've reloaded your shell configuration after installation
2. **R packages not installing**: Make sure post-link scripts are enabled with `pixi config set --local run-post-link-scripts true`
3. **Jupyter kernels not appearing**: Try reinstalling the kernels within the pixi environment
4. **Slow performance**: Ensure your project is stored in the WSL filesystem, not mounted Windows drives

## Next Steps

Once your environment is set up, you can:
- Create new notebooks in Jupyter Lab
- Install additional packages with `pixi add <package-name>`
- Share your environment configuration by committing the `pixi.toml` and `pixi.lock` files to version control
- Explore the extensive conda-forge and bioconda package ecosystems available through pixi
