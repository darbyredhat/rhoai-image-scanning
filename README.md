# RHOAI Image Scanning

**_NOTE: WORK IN PROGRESS_**

This repository contains a shell script designed to mirror OpenShift operator images, scan them for security vulnerabilities using Trivy, and generate a structured CSV report. This is particularly useful for analyzing the security posture of images intended for disconnected or air-gapped OpenShift environments.

-----

## Features

The script automates the following tasks:

  * **OpenShift Operator Mirroring**: Downloads all images associated with a specified OpenShift operator index version (e.g., `v4.17`, `v4.19`) using the `oc mirror` command.
  * **Local Storage**: Stores all mirrored content into a designated local directory.
  * **Image Discovery**: Identifies individual image references within the mirrored content.
  * **Vulnerability Scanning**: Runs Trivy scans on each discovered image.
  * **Filtered Reporting**: Parses Trivy's output, filtering results to include only **High** and **Critical** severity vulnerabilities.
  * **CSV Report Generation**: Creates a CSV file with the following four columns for each processed image:
      * **Registry**: The domain of the image registry (e.g., `registry.redhat.io`).
      * **Package**: The image's repository path (e.g., `openshift4/ose-oauth-proxy`).
      * **Image**: The image digest or tag (e.g., `sha256:4f8d66597feeb32bb18699326029f9a71a5aca4a57679d636b876377c2e95695`).
      * **Version**: The OpenShift operator index version that the image belongs to (e.g., `v4.17`).

-----

## Prerequisites

Before running the script, ensure you have the following installed:

  * **OpenShift CLI (oc)**:
      * [Install OpenShift CLI](https://www.google.com/search?q=https://docs.openshift.com/container-platform/latest/cli_reference/developer-cli-odo/installing-cli.html)
  * **oc-mirror plugin**:
      * Download the `oc-mirror` plugin from the Red Hat Hybrid Cloud Console and place it in your system's `PATH`.
      * Ensure your `~/.docker/config.json` is configured with valid pull secrets for `registry.redhat.io`.
  * **Trivy**:
      * [Install Trivy](https://aquasecurity.github.io/trivy/latest/getting-started/installation/)
  * **jq**: A lightweight and flexible command-line JSON processor. It's used for parsing Trivy's JSON output.
      * On Debian/Ubuntu: `sudo apt-get install jq`
      * On CentOS/RHEL: `sudo yum install jq`
      * On macOS (Homebrew): `brew install jq`

-----

## Usage

1.  **Clone the Repository** (or save the script):

    ```bash
    git clone <repository_url>
    cd rhoai-image-scanning
    ```

2.  **Make the script executable**:

    ```bash
    chmod +x scan_images.sh # Replace with your script's name
    ```

3.  **Run the script**:
    Provide the desired OpenShift operator index version as a command-line argument.

    ```bash
    ./scan_images.sh v4.17
    ```

    Replace `v4.17` with the specific OpenShift version you intend to mirror and scan (e.g., `v4.19`, `v4.20`).

-----

## Output

The script generates a CSV file named `trivy_vulnerabilities.csv` in the script's execution directory.

### CSV Column Details and Example:

For an image like `registry.redhat.io/openshift4/ose-oauth-proxy@sha256:4f8d66597feeb32bb18699326029f9a71a5aca4a57679d636b876377c2e95695` and an operator version `v4.17`, the CSV entry would look similar to:

| Column A (Registry)   | Column B (Package)        | Column C (Image)                                         | Column D (Version) |
| :-------------------- | :------------------------ | :------------------------------------------------------- | :----------------- |
| `registry.redhat.io`  | `openshift4/ose-oauth-proxy` | `sha256:4f8d66597feeb32bb18699326029f9a71a5aca4a57679d636b876377c2e95695` | `v4.17`            |

**Note**: If no High or Critical vulnerabilities are found for an image, it will still be listed in the CSV, but any additional vulnerability details would be absent.