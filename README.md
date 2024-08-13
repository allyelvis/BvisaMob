Here's a complete Bash script that sets up an environment to integrate Burundi mobile money providers with Visa services using the Visa Partner API. It uses the `curl` command for making API requests. The script also includes the use of environment variables for sensitive data and a README section for user instructions.

### Step 1: Create the Bash Script

Create a file named `burundi_visa_integration.sh`:

```bash
#!/bin/bash

# Script to integrate Burundi mobile money providers with Visa services

# Set environment variables
export VISA_API_KEY="your_visa_api_key"
export VISA_USER_ID="your_visa_user_id"
export VISA_PASSWORD="your_visa_password"
export VISA_CERT_PATH="./certs/visa_cert.pem"
export VISA_KEY_PATH="./certs/visa_key.pem"
export VISA_PASSPHRASE="your_cert_passphrase"

export ECOCASH_API_URL="https://api.ecocash.co.bi"
export LUMICASH_API_URL="https://api.lumicash.co.bi"
export MOBILE_MONEY_API_KEY="your_mobile_money_api_key"

# Function to transfer funds from mobile money to Visa card
transfer_mobile_to_visa() {
  local amount=$1
  local mobile_money_provider=$2
  local mobile_number=$3
  local visa_card_number=$4

  echo "Transferring $amount from $mobile_money_provider ($mobile_number) to Visa card $visa_card_number..."

  response=$(curl -s --cert $VISA_CERT_PATH --key $VISA_KEY_PATH --pass $VISA_PASSPHRASE \
    -X POST "https://sandbox.api.visa.com/visadirect/fundstransfer/v1/pushfundstransactions" \
    -H "Content-Type: application/json" \
    -H "Authorization: Basic $(echo -n "$VISA_USER_ID:$VISA_PASSWORD" | base64)" \
    -d '{
      "amount": "'"$amount"'",
      "recipientPrimaryAccountNumber": "'"$visa_card_number"'",
      "senderAccountNumber": "'"$mobile_number"'",
      "transactionCurrencyCode": "USD",
      "localTransactionDateTime": "2024-08-13T12:34:56",
      "senderName": "Mobile User"
    }')

  echo "Response: $response"
}

# Function to transfer funds from Visa card to mobile money
transfer_visa_to_mobile() {
  local amount=$1
  local visa_card_number=$2
  local mobile_money_provider=$3
  local mobile_number=$4

  echo "Transferring $amount from Visa card $visa_card_number to $mobile_money_provider ($mobile_number)..."

  mobile_money_api_url=$([ "$mobile_money_provider" == "ecocash" ] && echo "$ECOCASH_API_URL" || echo "$LUMICASH_API_URL")

  response=$(curl -s -X POST "$mobile_money_api_url/fundstransfer" \
    -H "Content-Type: application/json" \
    -H "Authorization: Bearer $MOBILE_MONEY_API_KEY" \
    -d '{
      "amount": "'"$amount"'",
      "senderAccountNumber": "'"$visa_card_number"'",
      "recipientMobileNumber": "'"$mobile_number"'",
      "transactionCurrencyCode": "BIF",
      "localTransactionDateTime": "2024-08-13T12:34:56"
    }')

  echo "Response: $response"
}

# Function to check Visa card balance
check_visa_balance() {
  local visa_card_number=$1

  echo "Checking balance for Visa card $visa_card_number..."

  response=$(curl -s --cert $VISA_CERT_PATH --key $VISA_KEY_PATH --pass $VISA_PASSPHRASE \
    -X GET "https://sandbox.api.visa.com/visadirect/fundstransfer/v1/pullfundstransactions/$visa_card_number/balance" \
    -H "Authorization: Basic $(echo -n "$VISA_USER_ID:$VISA_PASSWORD" | base64)")

  echo "Response: $response"
}

# Function to check mobile money balance
check_mobile_balance() {
  local mobile_money_provider=$1
  local mobile_number=$2

  echo "Checking balance for $mobile_money_provider ($mobile_number)..."

  mobile_money_api_url=$([ "$mobile_money_provider" == "ecocash" ] && echo "$ECOCASH_API_URL" || echo "$LUMICASH_API_URL")

  response=$(curl -s -X GET "$mobile_money_api_url/account/$mobile_number/balance" \
    -H "Authorization: Bearer $MOBILE_MONEY_API_KEY")

  echo "Response: $response"
}

# Menu for user interaction
while true; do
  echo "Choose an option:"
  echo "1. Transfer Mobile Money to Visa"
  echo "2. Transfer Visa to Mobile Money"
  echo "3. Check Visa Balance"
  echo "4. Check Mobile Money Balance"
  echo "5. Exit"
  read -p "Option: " option

  case $option in
    1)
      read -p "Enter amount: " amount
      read -p "Enter mobile money provider (ecocash/lumicash): " mobile_money_provider
      read -p "Enter mobile number: " mobile_number
      read -p "Enter Visa card number: " visa_card_number
      transfer_mobile_to_visa $amount $mobile_money_provider $mobile_number $visa_card_number
      ;;
    2)
      read -p "Enter amount: " amount
      read -p "Enter Visa card number: " visa_card_number
      read -p "Enter mobile money provider (ecocash/lumicash): " mobile_money_provider
      read -p "Enter mobile number: " mobile_number
      transfer_visa_to_mobile $amount $visa_card_number $mobile_money_provider $mobile_number
      ;;
    3)
      read -p "Enter Visa card number: " visa_card_number
      check_visa_balance $visa_card_number
      ;;
    4)
      read -p "Enter mobile money provider (ecocash/lumicash): " mobile_money_provider
      read -p "Enter mobile number: " mobile_number
      check_mobile_balance $mobile_money_provider $mobile_number
      ;;
    5)
      echo "Exiting..."
      exit 0
      ;;
    *)
      echo "Invalid option. Please try again."
      ;;
  esac
done
```

### Step 2: Make the Script Executable

```bash
chmod +x burundi_visa_integration.sh
```

### Step 3: Set Up the Certificates

- Create a directory `certs/` in the same location as your script.
- Place your Visa API certificate (`visa_cert.pem`) and private key (`visa_key.pem`) in the `certs/` directory.

### Step 4: Populate Environment Variables

- Replace placeholders in the script with your actual Visa Partner API credentials.
- Make sure to set the correct paths for the certificate and key files.

### Step 5: Running the Script

You can run the script by executing the following command in your terminal:

```bash
./burundi_visa_integration.sh
```

### Step 6: README Content

You can include the following in your `README.md` file:

```markdown
# Burundi Mobile Money to Visa Card Integration Script

This script allows you to perform various financial operations between Burundi mobile money providers (EcoCash, Lumicash) and Visa cards.

## Prerequisites

1. Bash shell (Linux or macOS).
2. Visa Partner API credentials.
3. Mobile Money API credentials.
4. SSL certificates from Visa Partner.

## Setup

1. **Clone the Repository**:
   ```bash
   git clone <repository-url>
   cd burundi-mobile-money-visa
   ```

2. **Place SSL Certificates**:
   Place your Visa API certificate and private key in the `certs/` directory.

3. **Edit the Script**:
   - Replace the placeholders in the script with your actual API credentials.

4. **Make the Script Executable**:
   ```bash
   chmod +x burundi_visa_integration.sh
   ```

5. **Run the Script**:
   ```bash
   ./burundi_visa_integration.sh
   ```

## Usage

- **Transfer Mobile Money to Visa**: Transfer funds from EcoCash or Lumicash to Visa card.
- **Transfer Visa to Mobile Money**: Transfer funds from Visa card to EcoCash or Lumicash.
- **Check Visa Balance**: Check balance for a Visa card.
- **Check Mobile Money Balance**: Check balance for EcoCash or Lumicash.

## License

This project is licensed under the MIT License.
```

This Bash script and README setup allow you to integrate Visa Partner services with Burundi mobile money providers. Make sure you have the necessary API credentials and SSL certificates to use this script effectively.
