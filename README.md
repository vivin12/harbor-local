# harbor-local
---

### **Prerequisites**

1. **Podman**: Install Podman on your MacBook.
2. **Podman Machine**: Set up Podman Machine to run containers on macOS.
3. **Podman Compose**: Install Podman Compose to handle Docker Compose files.
4. **Harbor Installation Files**: Download the Harbor package.

---

### **Steps to Install Harbor Using Podman on MacBook**

#### **1. Install Podman on macOS**

Install Podman using Homebrew:

```bash
brew install podman
```

Verify the installation:

```bash
podman --version
```

#### **2. Set Up Podman Machine**

Since macOS doesn't support containerization natively, Podman uses a virtual machine called Podman Machine.

Initialize and start Podman Machine:

```bash
podman machine init
podman machine start
```

Check the status:

```bash
podman machine list
```

#### **3. Install Podman Compose**

Podman Compose allows you to run `docker-compose.yml` files with Podman.

Install Podman Compose using pip3:

```bash
pip3 install podman-compose
```

Verify the installation:

```bash
podman-compose --version
```

> **Note**: Ensure you have Python 3 and pip installed.

#### **4. Download the Harbor Installation Files**

Download the latest Harbor release:

```bash
curl -LO https://github.com/goharbor/harbor/releases/download/v2.8.0/harbor-online-installer-v2.8.0.tgz
tar xzvf harbor-online-installer-v2.8.0.tgz
cd harbor
```

#### **5. Modify the `harbor.yml` Configuration File**

Edit `harbor.yml` to suit your setup:

```bash
vim harbor.yml
```

Key configurations:

- **Hostname**: Set to `localhost` or `127.0.0.1`.

  ```yaml
  hostname: localhost
  ```

- **Disable HTTPS** (for local development):

  Comment out the `https` section:

  ```yaml
  #https:
  #  port: 443
  #  certificate: /your/certificate/path
  #  private_key: /your/private/key/path
  ```

- **Enable HTTP**:

  ```yaml
  http:
    port: 8080
  ```

- **Admin Password**: Optionally change the default password.

  ```yaml
  harbor_admin_password: YourSecurePassword
  ```

#### **6. Prepare the Environment for Podman**

Harbor's `install.sh` script is designed for Docker, so we'll adapt the setup.

**a. Run the `prepare` Script**

Generate the `docker-compose.yml` file:

```bash
./prepare
```

**b. Modify `docker-compose.yml` for Podman**

Open `docker-compose.yml`:

```bash
vim docker-compose.yml
```

- **Change the Compose File Version**:

  ```yaml
  version: '3.7'
  ```

- **Replace Docker with Podman in Service Definitions**:

  Replace instances of `docker` with `podman` if necessary.

- **Adjust Image References**:

  Ensure image paths are correct and accessible.

**c. Set Environment Variables**

Set the timeout to prevent potential issues:

```bash
export COMPOSE_HTTP_TIMEOUT=500
```

#### **7. Start Harbor Using Podman Compose**

Start the Harbor services:

```bash
podman-compose up -d
```

This command:

- Builds and starts Harbor services.
- Pulls necessary images.

#### **8. Verify Harbor is Running**

List running containers:

```bash
podman ps
```

You should see Harbor's containers up and running.

#### **9. Access the Harbor Web Interface**

Open your browser and navigate to:

```
http://localhost:8080
```

Log in with:

- **Username**: `admin`
- **Password**: The password you set in `harbor.yml` (default is `Harbor12345`).

#### **10. Configure Podman to Use Harbor Registry**

To push and pull images, configure Podman to recognize your Harbor registry.

**a. Add Insecure Registry (if using HTTP)**

Edit or create `registries.conf`:

```bash
mkdir -p ~/.config/containers
vim ~/.config/containers/registries.conf
```

Add:

```toml
[registries.insecure]
registries = ['localhost:8080']
```

**b. Log in to the Harbor Registry**

```bash
podman login localhost:8080
```

Enter your Harbor credentials when prompted.

#### **11. Push and Pull Images with Podman**

**Tag an Image**:

```bash
podman tag your-image:latest localhost:8080/library/your-image:latest
```

**Push the Image**:

```bash
podman push localhost:8080/library/your-image:latest
```

**Pull the Image**:

```bash
podman pull localhost:8080/library/your-image:latest
```

#### **12. Managing Harbor Services**

**Stop Harbor**:

```bash
podman-compose down
```

**Start Harbor**:

```bash
podman-compose up -d
```

---

### **Additional Notes**

- **Podman Compatibility**: While Podman aims for Docker compatibility, some features may not work identically. Be prepared to troubleshoot.
- **Resource Allocation**: Podman Machine uses a VM, which may require adjusting CPU and memory settings.
- **SSL/TLS Configuration**: For secure setups, configure SSL certificates in `harbor.yml`.
- **Networking**: Ensure ports used by Harbor are not blocked by your system firewall.

---

### **Conclusion**

By following these steps, you've successfully installed Harbor on your MacBook using Podman. This setup allows you to manage container images locally without relying on Docker, aligning with your preference for using Podman.

If you encounter any issues or have further questions, feel free to ask!
