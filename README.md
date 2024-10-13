# 🚀 Fastlane Monorepo Setup

This configuration provides a unified and streamlined deployment pipeline for both platforms,
leveraging shared code and best practices to simplify your continuous integration and delivery processes.

**Note:** This concept can be applied to any monorepo setup and is not specific to [Nx](https://nx.dev).

## Table of Contents

- [Overview](#overview)
- [Prerequisites](#prerequisites)
- [Directory Structure](#directory-structure)
- [Setup Instructions](#setup-instructions)
- [Usage](#usage)
  - [iOS Deployment](#ios-deployment)
  - [Android Deployment](#android-deployment)
- [Environment Variable Management](#environment-variable-management)
- [Shared Lanes and Functions](#shared-lanes-and-functions)
- [Error Handling](#error-handling)
- [Contributing](#contributing)
- [License](#license)

## Overview

This monorepo setup utilizes Fastlane to manage the build and deployment processes for both iOS and Android applications.

It defines common lanes and shared functions to promote code reuse and consistency across platforms.

## Prerequisites

- **[Ruby](https://www.ruby-lang.org/en)** and **[Bundler](https://bundler.io)** installed.

- **[Fastlane](https://github.com/fastlane/fastlane)** installed (`gem install fastlane`).

- Appropriate **environment variables** and `.env.<ENVFILE>` files set up for different environments (e.g., `dev`, `prod`).

- **[Dotenv](https://github.com/bkeepers/dotenv)** Ruby gem installed (`gem install dotenv`) for loading environment variables from `.env` files.

## Directory Structure

All scripts are written in Ruby syntax by default.

```plaintext
.
├── fastlane
│   ├── Fastfile
│   ├── Appfile
│   └── setup
│       ├── deploy-impl-ios
│       ├── deploy-impl-android
│       └── shared
│           ├── checkout
│           ├── load-project-env-vars
│           ├── throw-user-error
│           └── validate-prerequisites
```

## Setup Instructions

1. **Clone the Repository**:

   ```bash
   git clone <your-repo-url>
   cd <your-repo-directory>
   ```

2. **Install Dependencies**:

   ```bash
   bundle install
   ```

3. **Configure Environment Variables**:

   - Create environment-specific `.env` files (e.g., `.env.dev`, `.env.prod`) in your project directory.

   - Create a `.env.fastlane` file for Fastlane-specific environment variables.

   - Ensure all required environment variables are defined.

4. **Set Up Fastlane Prerequisites**:

   - Define your prerequisites `fastlane-prerequisites-<environment>.json` files in your project directory.

     - Include required options and environment variables for validation.

   - Configure options and environment variables, refer private_lane of `setup_prerequisites`.

     - If you are using options, you need to pass them manually to actions in the subsequent lanes.

     - If you are using env vars, Fastlane will automatically load them.

## Usage

### iOS Deployment

- **Development Build**:

  ```bash
  fastlane ios deploy_dev project_path:<path-to-your-ios-project>
  ```

  Example:

  ```bash
  fastlane ios deploy_dev project_path:apps/app-1
  ```

- **Production Build**:

  ```bash
  fastlane ios deploy_prod project_path:<path-to-your-ios-project>
  ```

  Example:

  ```bash
  fastlane ios deploy_prod project_path:apps/app-1
  ```

### Android Deployment

- **Development Build**:

  ```bash
  fastlane android deploy_dev project_path:<path-to-your-android-project>
  ```

  Example:

  ```bash
  fastlane android deploy_dev project_path:apps/app-1
  ```

- **Production Build**:

  ```bash
  fastlane android deploy_prod project_path:<path-to-your-android-project>
  ```

  Example:

  ```bash
  fastlane android deploy_prod project_path:apps/app-1
  ```

## Environment Variable Management

By default, the setup loads two environment files using `dotenv`:

1. **`.env.fastlane`**: Contains environment variables needed by Fastlane.

2. **`.env.<ENVFILE>`**: Contains environment variables needed by the project, where `<ENVFILE>` is the specified environment (e.g., `dev`, `prod`).

## Shared Lanes and Functions

### `shared_checkout`

- **Purpose**: Sets up the project environment and executes the deployment implementation.

- **Functionality**:

  - Checks for the presence of the `project_path` option.

  - Validates the existence of the project directory.

  - Loads project-specific environment variables.

  - Changes the working directory to the project path.

  - Executes the deployment implementation within the project's context.

### `shared_load_project_env`

- **Purpose**: Loads environment variables from `.env` files using `dotenv`.

- **Functionality**:

  - Loads `.env.fastlane` for Fastlane-specific variables.

  - Loads `.env.<ENVFILE>` for project-specific variables.

  - Supports loading multiple environment files.

- **Usage Notes**:

  - Ensure that both `.env.fastlane` and `.env.<ENVFILE>` exist and contain the necessary variables.

  - The environment is specified by setting the `ENVFILE` environment variable before running Fastlane.

### `shared_throw_user_error`

- **Purpose**: Provides a standardized way to display error messages and exit execution.

- **Functionality**:

  - Takes an error message and an optional exception.

  - Outputs a formatted error message using Fastlane's `UI.user_error!`.

### `shared_validate_prerequisites`

- **Purpose**: Validates that all required options and environment variables are present before proceeding with the build.

- **Functionality**:

  - Loads prerequisite definitions from `fastlane-prerequisites-<ENVFILE>.json`.

  - Validates required options specified in the prerequisites file if needs.

  - Validates required environment variables specified in the prerequisites file if needs.

  - Prompts the user if the prerequisites file is missing, allowing them to continue or abort.

## Error Handling

- The setup includes robust error handling using the `error` block in Fastlane.

- Cleans up keychains and other resources in case of failures.

- Provides descriptive error messages to facilitate debugging.

- Utilizes the `shared_throw_user_error` function for consistent error reporting.
