# AWS Workshop: Bedrock Multi Agents and GenAI on AWS

## 🎯 Workshop Overview

This repository contains the complete implementation of an AWS Workshop focused on **Amazon Bedrock Multi Agents and Generative AI on AWS**. The workshop demonstrates how to build intelligent, conversational AI agents using Amazon Bedrock that can handle complex business scenarios through multiple action groups.

**Workshop Speaker**: _Pham Tien Thuan Phat_ - As featured in the workshop presentation slides

## 🏗️ Architecture Overview

This workshop implements a **Single Agent Architecture** with multiple action groups that can handle:

-   **Hotel Room Management** (Availability & Booking)
-   **HR Operations** (Leave Balance Inquiries)
-   **Knowledge Base Integration** (Policy Documents & Hotel Information)

```
┌─────────────────────────────────────────────────────────────┐
│                    Amazon Bedrock Agent                     │
├─────────────────────────────────────────────────────────────┤
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────┐ │
│  │ Hotel Room      │  │ Hotel Room      │  │ HR Action   │ │
│  │ Availability    │  │ Booking         │  │ Group       │ │
│  │ Action Group    │  │ Action Group    │  │             │ │
│  └─────────────────┘  └─────────────────┘  └─────────────┘ │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                    AWS Lambda Functions                     │
├─────────────────────────────────────────────────────────────┤
│  • Room Availability Lambda                                │
│  • Room Booking Lambda                                     │
│  • HR Leave Balance Lambda                                 │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                     Amazon DynamoDB                        │
├─────────────────────────────────────────────────────────────┤
│  • hotelRoomAvailabilityTable                             │
│  • hotelRoomBookingTable                                   │
│  • leaveBalanceHRTable                                     │
└─────────────────────────────────────────────────────────────┘
```

## 📁 Project Structure

```
Backend/
├── 📊 Slide/
│   └── Workshop GenAI on AWS.pptx          # Workshop presentation slides
├── 🏨 Hotel Room Availability Action Group/
│   ├── Hotel Room Availability_OpenAPISchema.yaml
│   └── hotelRoomAvailabilityFunction.zip
├── 🛏️ Hotel Room Booking Action Group/
│   ├── Hotel Room Booking_OpenAPISchema.yaml
│   ├── hotelRoomBookingFunction.zip
│   └── lambda_function.py
├── 👥 HR ActionGroup/
│   ├── lambda_function.py
│   ├── retrieveLeaveBalance_OpenAPISchema.yaml
│   ├── refactorhr_assist_leaveBalance.schema.yml
│   └── leaveBalanceHRFunction_Lambda.zip
├── 📚 Docs/
│   ├── Agent_InstructionsAgentBooking.pdf
│   ├── Knowledge Base _ HR Policy Document.pdf
│   ├── Supervisor Agent_Instructions.pdf
│   └── Taj-Fort-Aguada-Resort&SpaNew.pdf
└── README.md                               # This file
```

## 🚀 Features & Capabilities

### 🏨 Hotel Management System

-   **Room Availability Checking**: Real-time inventory management for Garden View and Sea View rooms
-   **Room Booking**: Complete booking workflow with unique booking ID generation
-   **Date-based Operations**: Handle check-in dates and duration calculations

### 👥 HR Management System

-   **Leave Balance Inquiries**: Employee leave balance retrieval by Employee ID
-   **Policy Integration**: Access to HR policy documents through knowledge base

### 🧠 AI Agent Capabilities

-   **Natural Language Processing**: Conversational interface for complex queries
-   **Multi-domain Expertise**: Handle both hospitality and HR operations
-   **Context Awareness**: Maintain conversation context across multiple interactions

## 🛠️ Technical Implementation

### Action Groups

#### 1. Hotel Room Availability Action Group

```yaml
# OpenAPI Schema for Room Availability
paths:
    /getRoomInventory/{date}:
        get:
            summary: Get overall hotel room inventory summary
            description: Returns summary of all room types and their availability
            operationId: getInventorySummary
```

**Features:**

-   Date-based room inventory queries
-   Garden View and Sea View room categorization
-   Real-time availability status

#### 2. Hotel Room Booking Action Group

```python
# Key Lambda Function Features:
- Unique booking ID generation using UUID
- DynamoDB integration for booking persistence
- Inventory validation before booking confirmation
- Comprehensive error handling for unavailable dates
```

**Booking Process:**

1. Validate room availability for requested date
2. Generate unique booking ID
3. Store booking details in DynamoDB
4. Return confirmation with booking reference

#### 3. HR Action Group

```python
# Employee Leave Balance Retrieval:
- Employee ID-based queries
- DynamoDB integration for HR data
- Structured response formatting for Bedrock Agent
```

### AWS Services Integration

#### Amazon Bedrock

-   **Foundation Models**: Leverages advanced LLMs for natural language understanding
-   **Agent Framework**: Orchestrates multiple action groups seamlessly
-   **Knowledge Base**: Integrates document repositories for enhanced responses

#### AWS Lambda

-   **Serverless Execution**: Cost-effective, scalable function execution
-   **Event-driven Architecture**: Responds to Bedrock Agent invocations
-   **Error Handling**: Comprehensive error management and logging

#### Amazon DynamoDB

-   **NoSQL Database**: Fast, flexible data storage
-   **Tables**:
    -   `hotelRoomAvailabilityTable`: Room inventory management
    -   `hotelRoomBookingTable`: Booking records storage
    -   `leaveBalanceHRTable`: Employee leave data

## 📋 Prerequisites

### AWS Account Setup

-   AWS Account with appropriate permissions
-   AWS CLI configured
-   Access to Amazon Bedrock service

### Required AWS Services

-   Amazon Bedrock (with Claude/Titan models enabled)
-   AWS Lambda
-   Amazon DynamoDB
-   Amazon S3 (for knowledge base documents)

### Development Environment

-   Python 3.9+
-   AWS SDK (Boto3)
-   OpenAPI 3.0 knowledge
-   Basic understanding of serverless architecture

## 🚀 Deployment Guide

### Step 1: DynamoDB Tables Setup

```bash
# Create Hotel Room Availability Table
aws dynamodb create-table \
    --table-name hotelRoomAvailabilityTable \
    --attribute-definitions AttributeName=date,AttributeType=S \
    --key-schema AttributeName=date,KeyType=HASH \
    --billing-mode PAY_PER_REQUEST

# Create Hotel Room Booking Table
aws dynamodb create-table \
    --table-name hotelRoomBookingTable \
    --attribute-definitions AttributeName=bookingID,AttributeType=S \
    --key-schema AttributeName=bookingID,KeyType=HASH \
    --billing-mode PAY_PER_REQUEST

# Create HR Leave Balance Table
aws dynamodb create-table \
    --table-name leaveBalanceHRTable \
    --attribute-definitions AttributeName=empID,AttributeType=N \
    --key-schema AttributeName=empID,KeyType=HASH \
    --billing-mode PAY_PER_REQUEST
```

### Step 2: Lambda Functions Deployment

```bash
# Deploy Hotel Room Booking Function
aws lambda create-function \
    --function-name hotelRoomBookingFunction \
    --runtime python3.9 \
    --role arn:aws:iam::YOUR_ACCOUNT:role/lambda-execution-role \
    --handler lambda_function.lambda_handler \
    --zip-file fileb://hotelRoomBookingFunction.zip

# Deploy HR Leave Balance Function
aws lambda create-function \
    --function-name leaveBalanceHRFunction \
    --runtime python3.9 \
    --role arn:aws:iam::YOUR_ACCOUNT:role/lambda-execution-role \
    --handler lambda_function.lambda_handler \
    --zip-file fileb://leaveBalanceHRFunction_Lambda.zip
```

### Step 3: Bedrock Agent Configuration

1. Create a new Bedrock Agent in AWS Console
2. Configure Action Groups with respective OpenAPI schemas
3. Associate Lambda functions with corresponding action groups
4. Set up Knowledge Base with policy documents
5. Test agent functionality

## 💡 Usage Examples

### Hotel Room Booking Conversation

```
User: "I need to book a sea view room for March 15th for 3 nights under the name John Doe"

Agent: "I'll help you book a sea view room for March 15th for 3 nights. Let me check availability and process your booking.

✅ Booking confirmed!
📋 Booking Details:
- Booking ID: a1b2c3d4-e5f6-7890-abcd-ef1234567890
- Guest Name: John Doe
- Check-in Date: March 15th
- Duration: 3 nights
- Room Type: Sea View

Your reservation is confirmed. Please save your booking ID for future reference."
```

### HR Leave Balance Inquiry

```
User: "What's the leave balance for employee ID 12345?"

Agent: "Let me retrieve the leave balance information for employee ID 12345.

📊 Leave Balance Summary:
- Annual Leave: 15 days remaining
- Sick Leave: 8 days remaining
- Personal Leave: 3 days remaining

Is there anything specific about the leave policy you'd like to know more about?"
```

## 🔧 Configuration Files

### OpenAPI Schemas

Each action group includes detailed OpenAPI 3.0 specifications:

-   **Hotel Room Availability**: Defines inventory query endpoints
-   **Hotel Room Booking**: Specifies booking creation parameters
-   **HR Operations**: Outlines employee data retrieval methods

### Lambda Function Structure

All Lambda functions follow the Bedrock Agent integration pattern:

```python
def lambda_handler(event, context):
    # 1. Extract parameters from Bedrock Agent event
    # 2. Process business logic
    # 3. Interact with DynamoDB
    # 4. Format response for Bedrock Agent
    # 5. Return structured response
```

## 📊 Workshop Learning Outcomes

### Technical Skills Developed

-   **Amazon Bedrock Agent Development**: End-to-end agent creation and configuration
-   **Serverless Architecture**: Lambda function design and deployment
-   **API Integration**: OpenAPI schema design and implementation
-   **Database Operations**: DynamoDB table design and operations
-   **AI/ML Integration**: Foundation model integration and prompt engineering

### Business Use Cases Covered

-   **Hospitality Industry**: Hotel booking and management systems
-   **Human Resources**: Employee self-service portals
-   **Customer Service**: Automated inquiry handling
-   **Multi-domain Operations**: Cross-functional AI assistants

## 🎓 Workshop Materials

### Presentation Slides

-   **Location**: `Slide/Workshop GenAI on AWS.pptx`
-   **Content**: Complete workshop walkthrough with architecture diagrams
-   **Speaker**: _[Your Name]_ - Featured throughout the presentation

### Documentation

-   **Agent Instructions**: Detailed configuration guides
-   **Knowledge Base Documents**: HR policies and hotel information
-   **API Specifications**: Complete OpenAPI documentation

## 🔍 Troubleshooting

### Common Issues

1. **Lambda Function Timeouts**: Increase timeout settings in Lambda configuration
2. **DynamoDB Access Errors**: Verify IAM roles have appropriate permissions
3. **Bedrock Agent Responses**: Check OpenAPI schema formatting
4. **Knowledge Base Integration**: Ensure documents are properly indexed

### Debug Tips

-   Enable CloudWatch logging for all Lambda functions
-   Use Bedrock Agent test console for debugging conversations
-   Monitor DynamoDB metrics for performance issues
-   Validate OpenAPI schemas using online validators

## 🤝 Contributing

This workshop codebase serves as a foundation for learning Amazon Bedrock Agents. Feel free to:

-   Extend functionality with additional action groups
-   Implement new business use cases
-   Enhance error handling and logging
-   Add monitoring and alerting capabilities

## 📞 Support & Resources

### AWS Documentation

-   [Amazon Bedrock User Guide](https://docs.aws.amazon.com/bedrock/)
-   [AWS Lambda Developer Guide](https://docs.aws.amazon.com/lambda/)
-   [Amazon DynamoDB Developer Guide](https://docs.aws.amazon.com/dynamodb/)

### Workshop Resources

-   **Slides**: Available in the `Slide/` directory
-   **Code Examples**: Complete implementation in respective action group folders
-   **Documentation**: Comprehensive guides in the `Docs/` directory

---

## 🏆 Workshop Credits

**Workshop Speaker & Developer**: _[Your Name]_

This workshop demonstrates the power of Amazon Bedrock Agents in creating sophisticated, multi-domain AI assistants that can handle complex business operations through natural language interactions.

**Built with ❤️ using Amazon Bedrock, AWS Lambda, and DynamoDB**

---

_Last Updated: June 2025_
_Workshop Version: 1.0_
