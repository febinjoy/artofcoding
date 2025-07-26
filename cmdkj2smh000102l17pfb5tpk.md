---
title: "Building a Context-Aware Gemini CLI Workflow"
seoTitle: "Gemini CLI Context Management: Complete Workflow Guide"
seoDescription: "Learn to build context management for Gemini CLI. Automated project selection, intelligent file loading, and seamless workflow like Claude Projects"
datePublished: Sat Jul 26 2025 17:32:27 GMT+0000 (Coordinated Universal Time)
cuid: cmdkj2smh000102l17pfb5tpk
slug: building-a-context-aware-gemini-cli-workflow
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1753550617260/fba4ea4d-0238-4449-8501-454af4b5cba4.png
tags: developer-tools, context-manager, gemini, google-gemini, ai-assistants, geminiai, google-gemini-ai, terminal-ai, ai-assistant, gemini-cli, context-management, geminicli, ai-cli

---

Have you ever wished you could have the same project-based context management in Gemini CLI that you get with Claude Projects or ChatGPT's custom instructions? After some experimentation, I've built a workflow that gives you exactly that - automated project selection, context management, and intelligent file loading for Gemini CLI.

## The Problem: Context Switching Overhead

When working with AI assistants across multiple projects, you constantly face the same frustrations:

* **Manual context setup** every time you start a new session
    
* **Forgetting to include** project-specific instructions or context
    
* **Inconsistent AI behaviour** due to missing context
    
* **Time wasted** navigating directories and copying instructions
    

ChatGPT offers custom GPTs, Claude provides Projects, and Perplexity has collections to handle project-specific context, but Gemini CLI lacks built-in project management features. This means you're stuck with manual setup every time.

Imagine you're a developer working on three different projects: a Go research project, a personal writing blog, and a technical documentation site. Each needs different context, tone, and specific instructions. Without proper setup, you'd spend the first few minutes of every Gemini CLI session explaining what you're working on.

## The Solution: Step-by-Step Setup

## Step 1: Create Your Gemini Root Folder

First, create a dedicated directory structure for your Gemini projects:

```bash
# Choose a location that works for you
mkdir -p ~/Dev/AI_Tools/Gemini
cd ~/Dev/AI_Tools/Gemini

# Create your global instructions file
touch instructions.md
```

Edit [`instructions.md`](http://instructions.md) with your personal Gemini preferences:

```markdown
# Global Gemini Instructions

- Communicate in a friendly, professional, and straightforward manner
- Provide actionable advice that is direct and avoids generic or weak phrasing
- Use UK English for all spelling, punctuation, and grammar
- Be specific and avoid vague responses
- When coding, prioritise readability and best practices
```

These are common instructions you want to provide to Gemini, irrespective of the project (folder) you are working on.

## Step 2: Create the [mygemini.sh](http://mygemini.sh) Script

Create the automation script that will handle project management:

```bash
# Create the script file
touch mygemini.sh
chmod +x mygemini.sh
```

Here's the complete script (compatible with both bash and zsh on Mac/Linux):

```bash
#!/usr/bin/env bash

# mygemini.sh - Context-aware Gemini CLI project manager
set -e

# Configuration - Update this path to match your setup
GEMINI_ROOT="$HOME/Dev/AI_Tools/Gemini"
INSTRUCTIONS_FILE="$GEMINI_ROOT/instructions.md"

# Colors for better UX (works on both Mac and Linux)
if [[ -t 1 ]]; then
    RED='\033[0;31m'
    GREEN='\033[0;32m'
    YELLOW='\033[1;33m'
    BLUE='\033[0;34m'
    CYAN='\033[0;36m'
    NC='\033[0m'
else
    RED='' GREEN='' YELLOW='' BLUE='' CYAN='' NC=''
fi

print_color() {
    local color=$1
    shift
    echo -e "${color}$*${NC}"
}

# Check prerequisites
if ! command -v gemini &> /dev/null; then
    print_color $RED "Error: 'gemini' command not found. Please install Gemini CLI first."
    exit 1
fi

if [[ ! -f "$INSTRUCTIONS_FILE" ]]; then
    print_color $RED "Error: instructions.md not found: $INSTRUCTIONS_FILE"
    print_color $YELLOW "Please create your global instructions file first."
    exit 1
fi

print_color $CYAN "🔮 MyGemini - Context-Aware Gemini"

# List all available projects
print_color $CYAN "\n=== Available Projects ==="
projects=()
counter=1

# Use shell-agnostic directory iteration
for item in "$GEMINI_ROOT"/*; do
    if [[ -d "$item" && "$(basename "$item")" != ".*" ]]; then
        project_name=$(basename "$item")
        projects+=("$project_name")
        print_color $BLUE "[$counter] $project_name"
        ((counter++))
    fi
done

print_color $GREEN "[$counter] Create New Project"
max_option=$counter

# Get user selection with validation
while true; do
    print_color $YELLOW "\nSelect a project (1-$max_option):"
    read -r selection
    
    if [[ "$selection" =~ ^[0-9]+$ ]] && [[ "$selection" -ge 1 ]] && [[ "$selection" -le "$max_option" ]]; then
        if [[ "$selection" -eq "$max_option" ]]; then
            # Create new project
            while true; do
                print_color $YELLOW "Enter new project name (no spaces or special characters):"
                read -r project_name
                
                # Validate project name
                if [[ -z "$project_name" ]]; then
                    print_color $RED "Project name cannot be empty."
                    continue
                elif [[ "$project_name" =~ [^a-zA-Z0-9_-] ]]; then
                    print_color $RED "Project name can only contain letters, numbers, underscores, and hyphens."
                    continue
                elif [[ -d "$GEMINI_ROOT/$project_name" ]]; then
                    print_color $RED "Project '$project_name' already exists."
                    continue
                else
                    mkdir -p "$GEMINI_ROOT/$project_name"
                    selected_project="$project_name"
                    break
                fi
            done
        else
            selected_project="${projects[$((selection-1))]}"
        fi
        break
    else
        print_color $RED "Invalid selection. Please enter a number between 1 and $max_option."
    fi
done

project_path="$GEMINI_ROOT/$selected_project"
context_file="$project_path/context.md"

print_color $GREEN "✓ Selected: $selected_project"

# Handle project context
if [[ -f "$context_file" ]]; then
    print_color $GREEN "✓ Found existing context.md"
else
    print_color $YELLOW "No context.md found for this project."
    print_color $CYAN "Please provide project context (type 'END' on a new line when finished):"
    
    context_content=""
    while IFS= read -r line; do
        if [[ "$line" == "END" ]]; then
            break
        fi
        context_content+="$line"$'\n'
    done
    
    if [[ -n "$context_content" ]]; then
        printf "%s" "$context_content" > "$context_file"
        print_color $GREEN "✓ Context saved to $context_file"
    else
        printf "# Context for %s\n\nAdd your project-specific context here." "$selected_project" > "$context_file"
        print_color $YELLOW "✓ Created placeholder context file"
    fi
fi

# Copy global instructions to project folder (always get latest version)
cp "$INSTRUCTIONS_FILE" "$project_path/instructions.md"
print_color $YELLOW "✓ Global instructions copied to project"

# Launch Gemini in project directory
cd "$project_path"
print_color $CYAN "Working directory: $project_path"
print_color $GREEN "\nLaunching Gemini CLI with project context...\n"

exec gemini
```

## Step 3: Add Shell Alias

Add an alias to your shell configuration file for easy access:

**For Zsh users (~/.zshrc):**

```bash
echo 'alias mygemini="~/Dev/AI_Tools/Gemini/mygemini.sh"' >> ~/.zshrc
source ~/.zshrc
```

**For Bash users (~/.bashrc or ~/.bash\_profile):**

```bash
echo 'alias mygemini="~/Dev/AI_Tools/Gemini/mygemini.sh"' >> ~/.bashrc
source ~/.bashrc
```

## Step 4: Configure Gemini's Memory System

Navigate to your Gemini root directory and set up automatic file loading:

```bash
cd ~/Dev/AI_Tools/Gemini
gemini
```

In the Gemini CLI, run these commands once:

```bash
/memory add instructions.md
/memory add context.md
```

These commands tell Gemini to automatically load these files whenever they exist in the current working directory. This is the key that makes the entire workflow seamless.

## Step 5: How It Works

Instead of running `gemini`, use the command you added as alias `mygemini`.

Here's the magic behind the workflow:

1. The alias in your **bashrc/zshrc** will trigger the `mygemini.sh` script.
    
2. **Script launches** and presents your project menu
    
3. **You select** an existing project or create a new one
    
4. **Context handling** occurs automatically:
    
    * Existing projects: Loads saved context
        
        * If no context.md is found, it asks for context and creates it
            
    * New projects: Prompts you to create context
        
5. **Global instructions** are copied to the project folder
    
6. **Gemini launches** in the project directory
    
7. **Automatic loading** happens thanks to the `/memory add` commands
    
8. **Gemini is ready** with full context and your preferences
    

The beauty is that steps 7-8 happen automatically every time, with no manual intervention required.

## Step 6: Verify It's Working

To confirm everything is working correctly, try this verification process:

1. **Create a test project:**
    
    ```bash
    mygemini
    # Select "Create New Project"
    # Name it "Test_Project"
    # Add context: "This is a test project for verification"
    ```
    
2. **In the Gemini CLI that opens, ask:**
    
    ```plaintext
    What are your instructions and context for this session?
    ```
    
3. **Expected response should include:**
    
    * Your global instructions (UK English, professional tone, etc.)
        
    * The project context ("This is a test project for verification")
        
    * Confirmation that both files were loaded automatically
        
4. **Test with different projects** to ensure context switches correctly
    

## Step 7: Sample Directory Structure

After setting up a few projects, your directory structure should look like this:

```plaintext
~/Dev/AI_Tools/Gemini/
├── instructions.md              # Global Gemini instructions
├── mygemini.sh                  # The automation script
├── Blogs/
│   ├── context.md              # "Help me write engaging blog posts..."
│   └── instructions.md         # (copied automatically)
├── Personal_Writing/
│   ├── context.md              # "Assist with creative writing projects..."
│   └── instructions.md         # (copied automatically)
├── Research_Programming_Go/
│   ├── context.md              # "Go programming research and code review..."
│   └── instructions.md         # (copied automatically)
├── Client_ProjectX/
│   ├── context.md              # "Web development project using React..."
│   └── instructions.md         # (copied automatically)
└── Documentation/
    ├── context.md              # "Technical documentation and API guides..."
    └── instructions.md         # (copied automatically)
```

## Key Advantages

This workflow provides several powerful benefits:

### **🚀 Zero Setup Time**

No more typing "Act as a..." or explaining your preferences every session. Gemini is immediately ready with your context.

### **🎯 Project-Specific Intelligence**

Each project gets tailored context while maintaining your global preferences for tone, style, and language.

### **🔄 Consistent Behaviour**

Global instructions ensure Gemini behaves consistently across all projects, while adapting to specific contexts.

### **📁 Scalable Organisation**

Easy to add new projects as your work evolves. Each project maintains its own context while sharing global settings.

### **⚡ Automatic Updates**

Changes to global instructions automatically propagate to all projects on next launch.

### **🔍 Context Persistence**

Project contexts are saved and automatically reloaded, building a knowledge base over time.

## Extended Use Cases

This workflow adapts to numerous scenarios:

### **Software Development**

* **Frontend Projects**: Context about specific frameworks, design systems, coding standards
    
* **Backend Services**: API specifications, database schemas, architectural decisions
    
* **DevOps Tasks**: Infrastructure details, deployment procedures, monitoring requirements
    

### **Content Creation**

* **Blog Writing**: Target audience, brand voice, content guidelines
    
* **Technical Documentation**: Product specifications, user personas, complexity levels
    
* **Marketing Copy**: Brand guidelines, campaign objectives, target demographics
    

### **Research & Analysis**

* **Academic Research**: Research questions, methodology, literature review status
    
* **Market Research**: Industry focus, competitor analysis, data sources
    
* **Code Reviews**: Project standards, security requirements, performance criteria
    

### **Personal Projects**

* **Learning New Technologies**: Current skill level, learning objectives, preferred resources
    
* **Side Projects**: Project goals, technology stack, timeline constraints
    
* **Creative Writing**: Genre preferences, character development, plot outlines
    

### **Client Work**

* **Project Alpha**: Client requirements, technical constraints, delivery timeline
    
* **Project Beta**: Different tech stack, coding standards, communication preferences
    
* **Maintenance Work**: Legacy codebase context, known issues, update procedures
    

## Conclusion

By combining a simple shell script with Gemini's built-in memory system, we've created a CLI workflow that rivals the project management features of web-based AI interfaces. This approach eliminates the friction of context switching while maintaining the power and flexibility of command-line tools.

The key insights that make this work are:

1. **Separation of concerns**: Global preferences vs. project-specific context
    
2. **Automation**: Eliminate manual setup through scripting
    
3. **Leveraging built-in features**: Using Gemini's memory system rather than fighting it
    
4. **Consistent structure**: Predictable file organisation that scales
    

This workflow transforms Gemini from a generic tool into a context-aware partner that understands your work, your preferences, and your current focus. Whether you're switching between client projects, exploring new technologies, or working on personal creative endeavours, Gemini is immediately ready to help with the right context and tone.

The next time you need AI assistance using Gemini, you won't waste time explaining your situation or setting preferences. Instead, you'll jump straight into productive conversations with Gemini with preloaded context of your project, your standards, and your goals.

*Please don't hesitate to leave a comment on how you are using this in your workflows.*