# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Development Commands

### Building and Testing
- `make build` - Build the project
- `make test` - Run unit tests (tests all packages in `./pkg/...`)
- `make test-integration` - Run integration tests with kubebuilder
- `make verify` - Run linting with golangci-lint
- `make verify-gocilint` - Run just the golangci-lint check

### Images and Deployment
- `make images` - Build Docker images for addon-manager and addon-examples
- `make deploy-addon-manager` - Deploy addon manager to Kubernetes cluster
- `make deploy-helloworld` - Deploy helloworld example addon
- `make deploy-helloworld-helm` - Deploy helloworld-helm example addon
- `make deploy-helloworld-hosted` - Deploy helloworld-hosted example addon

### End-to-End Testing
- `make build-e2e` - Build e2e test binary
- `make test-e2e` - Run complete e2e tests (includes OCM setup and addon deployment)
- `make build-hosted-e2e` - Build hosted e2e test binary
- `make test-hosted-e2e` - Run hosted e2e tests

### Cleanup
- `make undeploy-addon` - Remove all addon CRs
- `make undeploy-helloworld` - Remove helloworld addon
- `make undeploy-helloworld-helm` - Remove helloworld-helm addon
- `make clean-integration-test` - Clean up integration test artifacts

## Project Architecture

This is an AddOn Framework library for Open Cluster Management (OCM) that provides infrastructure for developing and managing cluster addons.

### Core Components

**AddOn Factory (`pkg/addonfactory/`)**
- `AgentAddonFactory` - Main factory for building addon agents
- Template-based addons (`template_agentaddon.go`) - Use Go templates for manifests
- Helm-based addons (`helm_agentaddon.go`) - Use Helm charts for manifests
- Both support deployment configuration and value injection

**AddOn Manager (`pkg/addonmanager/`)**
- Hub-side management of addon agents across managed clusters
- Controllers for installation, configuration, health checking, and certificate management
- Supports both default and hosted deployment modes

**Manager (`pkg/manager/`)**
- Higher-level management plane for addon lifecycle
- Handles placement-based addon deployment and configuration propagation

**Base Controller (`pkg/basecontroller/`)**
- Common controller infrastructure and event handling
- Provides factory patterns for building controllers

### Key Interfaces

**AgentAddon (`pkg/agent/interface.go`)**
- Core interface that addon implementations must satisfy
- Handles manifests generation, health checking, and configuration

**AddonManager (`pkg/addonmanager/manager.go`)**
- Manages multiple addon agents and triggers reconciliation
- Entry point for registering and starting addon controllers

### Examples Structure

The `examples/` directory contains three addon implementations:
- `helloworld/` - Template-based addon using Go templates
- `helloworld_helm/` - Helm chart-based addon
- `helloworld_hosted/` - Hosted deployment mode addon

Each example includes deployment manifests, controllers, and agent implementations.

## Environment Variables

Important environment variables for development:
- `KUBECONFIG` - Path to kubeconfig file (default: `./.kubeconfig`)
- `MANAGER_IMAGE_NAME` - Addon manager image name
- `EXAMPLE_IMAGE_NAME` - Example addon image name
- `MANAGED_CLUSTER_NAME` - Name of managed cluster (default: `hub`)

## Integration with OCM

This framework requires Open Cluster Management (OCM) to be installed. Addons work with:
- `ManagedClusterAddOn` - Represents addon instance on a managed cluster
- `ClusterManagementAddOn` - Hub-side addon template and configuration
- `AddOnDeploymentConfig` - Configuration for addon deployment (node selectors, tolerations, etc.)