terraform {
  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "=3.107.0"
    }
  }
}
provider "azurerm" {
  features {}
}

# -----------------------
# Resource Group
# -----------------------
resource "azurerm_resource_group" "rg" {
  name     = "paru-rg"
  location = "East Us"
}

# -----------------------
# Virtual Network
# -----------------------
resource "azurerm_virtual_network" "vnet" {
  name                = "paru-vnet"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  address_space       = ["10.0.0.0/16"]
}

# -----------------------
# Subnet
# -----------------------
resource "azurerm_subnet" "subnet" {
  name                 = "paru-subnet"
  resource_group_name  = azurerm_resource_group.rg.name
  virtual_network_name = azurerm_virtual_network.vnet.name
  address_prefixes     = ["10.0.1.0/24"]
}

# -----------------------
# Network Security Group
# -----------------------
resource "azurerm_network_security_group" "nsg" {
  name                = "paru-nsg"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
}

# Allow SSH
resource "azurerm_network_security_rule" "ssh" {
  name                        = "Allow-SSH"
  priority                    = 100
  direction                   = "Inbound"
  access                      = "Allow"
  protocol                    = "Tcp"
  source_port_range           = "*"
  destination_port_range      = "22"
  source_address_prefix       = "*"
  destination_address_prefix  = "*"
  resource_group_name         = azurerm_resource_group.rg.name
  network_security_group_name = azurerm_network_security_group.nsg.name
  depends_on                  = [azurerm_network_security_group.nsg]
}

# -----------------------
# Public IP
# -----------------------
resource "azurerm_public_ip" "pip" {
  name                = "paru-public-ip"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  allocation_method   = "Static"
  sku                 = "Standard"
}

# -----------------------
# Network Interface
# -----------------------
resource "azurerm_network_interface" "nic" {
  name                = "paru-nic"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name

  ip_configuration {
    name                          = "internal"
    subnet_id                     = azurerm_subnet.subnet.id
    private_ip_address_allocation = "Dynamic"
    public_ip_address_id          = azurerm_public_ip.pip.id
  }
}

# NSG Association
resource "azurerm_network_interface_security_group_association" "nsglink" {
  network_interface_id      = azurerm_network_interface.nic.id
  network_security_group_id = azurerm_network_security_group.nsg.id
}

# -----------------------
# Random suffix
# -----------------------
resource "random_integer" "suffix" {
  min = 10000
  max = 99999
}

# -----------------------
# Storage Account
# -----------------------
resource "azurerm_storage_account" "storage" {
  name                     = "paruayi77${random_integer.suffix.result}"
  resource_group_name      = azurerm_resource_group.rg.name
  location                 = azurerm_resource_group.rg.location
  account_tier             = "Standard"
  account_replication_type = "LRS"
}

# -----------------------
# Virtual Machine
# -----------------------
resource "azurerm_linux_virtual_machine" "vm" {
  name                = "paru-vm"
  resource_group_name = azurerm_resource_group.rg.name
  location            = azurerm_resource_group.rg.location
  size                = "Standard D2s v3"

  admin_username = "paruadmin"
  admin_password = var.admin_password

  disable_password_authentication = false

  network_interface_ids = [
    azurerm_network_interface.nic.id,
  ]

  os_disk {
    caching              = "ReadWrite"
    storage_account_type = "Standard_LRS"
  }

  source_image_reference {
    publisher = "Canonical"
    offer     = "0001-com-ubuntu-server-focal"
    sku       = "20_04-lts"
    version   = "latest"
  }
}

# -----------------------
# Variables
# -----------------------
variable "admin_password" {
  description = "Admin password for VM"
  type        = string
  sensitive   = true