# Burundi Mobile Money to Visa Card Integration

This project provides a Bash script to facilitate financial transactions between Burundi mobile money providers (EcoCash, Lumicash) and Visa cards. The script supports operations such as transferring funds and checking balances.

## Prerequisites

Before you can use the script, make sure you have the following prerequisites:

1. **Bash Shell**: Ensure you have a Bash shell environment. This script is intended for use on Linux or macOS systems.

2. **Visa Partner API Credentials**: You'll need your Visa Partner API key, user ID, password, and SSL certificates.

3. **Mobile Money API Credentials**: Obtain API credentials from Burundi mobile money providers (EcoCash and Lumicash).

4. **OpenSSL**: Required for generating and handling SSL certificates.

## Setup

### Step 1: Clone the Repository

```bash
git clone <repository-url>
cd burundi-mobile-money-visa