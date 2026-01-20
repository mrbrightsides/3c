# 🏗️ RANTAI 3C - System Architecture

## Table of Contents
- [Overview](#overview)
- [System Architecture](#system-architecture)
- [Frontend Architecture](#frontend-architecture)
- [Blockchain Architecture](#blockchain-architecture)
- [Data Flow](#data-flow)
- [Smart Contract Design](#smart-contract-design)
- [Payment Processing](#payment-processing)
- [Storage Strategy](#storage-strategy)
- [Security Considerations](#security-considerations)
- [Scalability & Performance](#scalability--performance)
- [Integration Patterns](#integration-patterns)

---

## Overview

RANTAI 3C is a decentralized carbon management platform that combines Web3 technologies with cloud infrastructure monitoring. The architecture is designed around three core pillars:

- **Cloud**: Data ingestion and validation layer
- **Climate**: Carbon calculation and analytics engine
- **Chain**: Blockchain certification and immutable record-keeping

The platform employs a hybrid architecture that balances decentralization benefits with practical usability, enabling both traditional payment methods and cryptocurrency transactions.

---

## System Architecture

### High-Level Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                         User Interface                          │
│                    (Next.js 14 + TypeScript)                    │
└─────────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────────┐
│                      Application Layer                          │
├─────────────────┬─────────────────┬──────────────────┬──────────┤
│  Data Upload    │  Carbon Engine  │  Blockchain      │  Payment │
│  & Validation   │  & Analytics    │  Integration     │  Gateway │
└─────────────────┴─────────────────┴──────────────────┴──────────┘
                              ↓
┌─────────────────────────────────────────────────────────────────┐
│                      Persistence Layer                          │
├─────────────────┬─────────────────┬──────────────────┬──────────┤
│  LocalStorage   │  IPFS           │  Smart Contracts │  Oracle  │
│  (Client-side)  │  (Decentralized)│  (Sepolia)       │  Feeds   │
└─────────────────┴─────────────────┴──────────────────┴──────────┘
```

### Component Architecture

The application follows a modular, component-based architecture with clear separation of concerns:

```
Frontend Layer
├── UI Components (ShadCN/Radix UI)
├── Business Logic Components
│   ├── Data Upload & Validation
│   ├── Carbon Analysis Engine
│   ├── Blockchain Certification
│   └── Offset Marketplace
├── State Management (React Hooks)
└── Utility Functions

Blockchain Layer
├── Smart Contract Suite
│   ├── Carbon Records Registry
│   ├── NFT Achievement System (ERC-721)
│   ├── Carbon Credit Tokens (ERC-20)
│   ├── DAO Governance
│   └── Payment Processing
├── Web3 Integration (ethers.js v6)
├── SIWE Authentication
└── IPFS Storage

External Integrations
├── Cloud Providers (AWS, GCP, Azure)
├── PayPal Payment Gateway
└── Chainlink Oracle (Price Feeds)
```

---

## Frontend Architecture

### Technology Stack

**Core Framework**: Next.js 14 with App Router
- Server-side rendering for optimal performance
- Static generation for public pages
- Client-side hydration for interactive components

**Type Safety**: TypeScript in strict mode
- Comprehensive type definitions for all data structures
- Interface-based component design
- Type-safe blockchain interactions

**Styling System**: Tailwind CSS + ShadCN UI
- Utility-first CSS framework
- Accessible UI primitives from Radix UI
- Consistent design tokens

### Component Structure

```typescript
// Core Component Pattern
interface ComponentProps {
  data: TypedData;
  onAction: (result: TypedResult) => void;
}

const Component: React.FC<ComponentProps> = ({ data, onAction }) => {
  // Local state management
  const [state, setState] = useState<StateType>(initialState);
  
  // Side effects
  useEffect(() => {
    // Data fetching, subscriptions
  }, [dependencies]);
  
  // Event handlers
  const handleAction = useCallback(() => {
    // Business logic
    onAction(result);
  }, [dependencies]);
  
  return (/* JSX */);
};
```

### State Management Strategy

**Local State**: React hooks for component-specific state
- `useState` for simple state
- `useReducer` for complex state machines
- `useCallback` for memoized callbacks

**Persistent State**: Browser LocalStorage
- Historical carbon data
- User preferences
- Cached blockchain records

**Global State**: Context API (minimal usage)
- Wallet connection status
- User authentication state
- Theme preferences

### Routing & Navigation

```
/                           → Main application
  ├── Cloud (Tab)          → Data upload & validation
  ├── Auto Pull (Tab)      → Automated data fetching
  ├── Climate (Tab)        → Carbon analysis
  ├── Chain (Tab)          → Blockchain certification
  ├── Offset (Tab)         → Carbon offset marketplace
  └── About (Tab)          → Platform information
```

Tab-based navigation with deep-linking support and state preservation across navigation.

---

## Blockchain Architecture

### Network Configuration

**Primary Network**: Ethereum Sepolia Testnet
- Chain ID: 11155111
- RPC: `https://sepolia.infura.io/v3/f8d248f838ec4f12b0f01efd2b238206`
- Block time: ~12 seconds
- Gas token: SepoliaETH

**Rationale**: Sepolia chosen for:
- Active development support
- Stable testnet environment
- Faucet availability
- EVM compatibility

### Smart Contract Suite

#### 1. Carbon Records Registry

**Address**: `0x874378E56D92a0C4633b27A1730AD0CF8e7b4891`

**Purpose**: Immutable storage of carbon footprint records

**Data Structure**:
```solidity
struct CarbonRecord {
    uint256 recordId;
    address owner;
    uint256 carbonValue;      // in kg CO2e
    string dataHash;          // IPFS hash
    uint256 timestamp;
    bool verified;
}
```

**Key Functions**:
- `storeRecord(string dataHash, uint256 carbonValue)`: Store new carbon record
- `getRecord(uint256 recordId)`: Retrieve record details
- `verifyRecord(uint256 recordId)`: Mark record as verified
- `totalRecords()`: Get total count of records

**Events**:
```solidity
event RecordStored(
    uint256 indexed recordId,
    address indexed owner,
    uint256 carbonValue,
    string dataHash,
    uint256 timestamp
);

event RecordVerified(uint256 indexed recordId, address indexed verifier);
```

#### 2. NFT Achievement System (ERC-721)

**Purpose**: Tokenized achievement certificates for sustainability milestones

**Achievement Types**:
- Carbon Tracker: First carbon footprint recorded
- Data Collector: Multiple data uploads
- Blockchain Pioneer: First blockchain certification
- Offset Champion: Carbon offset purchases

**Metadata Structure**:
```json
{
  "name": "Achievement Name",
  "description": "Achievement description",
  "image": "ipfs://QmHash...",
  "attributes": [
    {
      "trait_type": "Type",
      "value": "Carbon Tracker"
    },
    {
      "trait_type": "Date Earned",
      "value": "2024-01-15"
    },
    {
      "trait_type": "Carbon Amount",
      "value": "1250 kg CO2e"
    }
  ]
}
```

**Functions**:
- `mintAchievement(address to, string achievementType)`: Mint achievement NFT
- `mintOffsetCertificate(address to, uint256 offsetAmount, string ipfsHash)`: Mint offset certificate
- `tokenURI(uint256 tokenId)`: Get IPFS metadata URI
- `balanceOf(address owner)`: Get NFT count
- `ownerOf(uint256 tokenId)`: Get NFT owner

#### 3. Carbon Credit Tokens (ERC-20)

**Purpose**: Tokenized carbon credits for corporate trading

**Token Specifications**:
- Name: Carbon Credit Token
- Symbol: CRB
- Decimals: 18
- Supply: Mintable (controlled)
- 1 CRB = 1 ton CO₂ equivalent

**Functions**:
- `mint(address to, uint256 amount)`: Issue new credits (admin only)
- `burn(uint256 amount)`: Retire carbon credits
- `transfer(address to, uint256 amount)`: Transfer credits
- `approve(address spender, uint256 amount)`: Approve spending
- `transferFrom(address from, address to, uint256 amount)`: Delegated transfer

**Use Cases**:
- Corporate carbon credit trading
- Offset verification tokens
- DAO voting weight
- Staking for governance

#### 4. DAO Governance Contract

**Purpose**: Decentralized decision-making for offset project selection

**Governance Model**:
- Token-weighted voting (1 CRB = 1 vote)
- Proposal threshold: 1000 CRB
- Voting period: 7 days
- Execution delay: 2 days (timelock)

**Proposal Types**:
- New offset project approval
- Platform fee adjustments
- Feature upgrades
- Treasury allocations

**Data Structure**:
```solidity
struct Proposal {
    uint256 proposalId;
    address proposer;
    string description;
    uint256 forVotes;
    uint256 againstVotes;
    uint256 startTime;
    uint256 endTime;
    bool executed;
    mapping(address => bool) hasVoted;
}
```

**Functions**:
- `createProposal(string description)`: Create new proposal
- `vote(uint256 proposalId, bool support)`: Cast vote
- `executeProposal(uint256 proposalId)`: Execute passed proposal
- `getProposal(uint256 proposalId)`: Get proposal details

#### 5. Carbon Offset Payment Contract

**Address**: `0x619971f4F2ED840fB0fCD344c95fc90BE1037c44`

**Purpose**: Dual payment system for carbon offset purchases

**Functions**:
- `purchaseOffset(string projectId, uint256 offsetAmount, string ipfsHash) payable`: Purchase offset with ETH
- `withdrawFunds()`: Owner withdrawal (platform fees)
- `getPurchaseHistory(address buyer)`: Get purchase history
- `getTotalOffsetAmount()`: Get total platform offset
- `getTotalPurchases()`: Get total purchase count

**Events**:
```solidity
event OffsetPurchased(
    address indexed buyer,
    string projectId,
    uint256 offsetAmount,
    uint256 amountPaid,
    uint256 timestamp
);

event FundsWithdrawn(address indexed recipient, uint256 amount);
```

**Payment Flow**:
1. User selects offset amount and project
2. Contract calculates cost (Oracle price × amount)
3. User sends ETH via `purchaseOffset()`
4. Contract emits `OffsetPurchased` event
5. NFT certificate minted automatically
6. Purchase recorded in IPFS
7. DAO treasury receives portion of payment

---

## Data Flow

### Carbon Calculation Pipeline

```
Data Input
    ↓
┌─────────────────────┐
│  File Upload (CSV)  │
│  or JSON Import     │
└─────────────────────┘
    ↓
┌─────────────────────┐
│  Data Validation    │
│  - Format check     │
│  - Required fields  │
│  - Quality scoring  │
└─────────────────────┘
    ↓
┌─────────────────────┐
│  Carbon Calculation │
│  - Emission factors │
│  - Regional grid    │
│  - Fuel types       │
└─────────────────────┘
    ↓
┌─────────────────────┐
│  Analytics Engine   │
│  - AI insights      │
│  - Predictions      │
│  - Benchmarking     │
└─────────────────────┘
    ↓
┌─────────────────────┐
│  Visualization      │
│  - Interactive      │
│  - Charts           │
│  - Reports          │
└─────────────────────┘
    ↓
┌─────────────────────┐
│  Blockchain         │
│  Certification      │
│  - IPFS storage     │
│  - Smart contract   │
│  - NFT minting      │
└─────────────────────┘
```

### Blockchain Certification Flow

```
User Action
    ↓
┌─────────────────────┐
│  Connect Wallet     │
│  (MetaMask)         │
└─────────────────────┘
    ↓
┌─────────────────────┐
│  SIWE               │
│  Authentication     │
│  - Sign message     │
│  - Verify signature │
└─────────────────────┘
    ↓
┌─────────────────────┐
│  Prepare Data       │
│  - Calculate hash   │
│  - Format metadata  │
└─────────────────────┘
    ↓
┌─────────────────────┐
│  Upload to IPFS     │
│  - Raw data         │
│  - Metadata         │
│  - Certificate      │
└─────────────────────┘
    ↓
┌─────────────────────┐
│  Smart Contract     │
│  Transaction        │
│  - storeRecord()    │
│  - Gas estimation   │
│  - Confirmation     │
└─────────────────────┘
    ↓
┌─────────────────────┐
│  Certificate        │
│  Generation         │
│  - Download JSON    │
│  - NFT minting      │
└─────────────────────┘
```

### Offset Purchase Flow

```
User Selection
    ↓
┌─────────────────────┐
│  Select Project &   │
│  Offset Amount      │
└─────────────────────┘
    ↓
┌─────────────────────┐
│  Price Calculation  │
│  - Oracle fetch     │
│  - Dynamic pricing  │
│  - Fee calculation  │
└─────────────────────┘
    ↓
┌─────────────────────┐
│  Payment Method     │
│  Selection          │
│  - Crypto (ETH)     │
│  - PayPal (Fiat)    │
└─────────────────────┘
    ↓
┌─────────────────────┐
│  Payment Processing │
│  - Crypto: Contract │
│  - PayPal: API      │
└─────────────────────┘
    ↓
┌─────────────────────┐
│  Verification       │
│  - Transaction hash │
│  - Payment ID       │
└─────────────────────┘
    ↓
┌─────────────────────┐
│  NFT Certificate    │
│  Minting            │
│  - Auto-mint        │
│  - IPFS metadata    │
└─────────────────────┘
    ↓
┌─────────────────────┐
│  Achievement Update │
│  - Badge unlock     │
│  - Level progress   │
└─────────────────────┘
```

---

## Payment Processing

### Dual Payment Architecture

The platform implements a hybrid payment system supporting both cryptocurrency and traditional payment methods.

#### Crypto Payment Flow

**Technology**: ethers.js v6 + MetaMask

**Process**:
1. User connects wallet via MetaMask
2. Contract calculates required ETH amount
3. Gas estimation performed
4. User confirms transaction
5. Smart contract emits `OffsetPurchased` event
6. Frontend listens for event confirmation
7. NFT certificate auto-minted
8. Transaction recorded on-chain

**Code Example**:
```typescript
const contract = new ethers.Contract(
  OFFSET_CONTRACT_ADDRESS,
  OFFSET_ABI,
  signer
);

const tx = await contract.purchaseOffset(
  projectId,
  ethers.parseUnits(offsetAmount.toString(), 18),
  ipfsHash,
  { value: ethers.parseEther(totalCost.toString()) }
);

await tx.wait(); // Wait for confirmation
```

#### PayPal Payment Flow

**Integration**: PayPal JavaScript SDK

**Process**:
1. User selects PayPal payment option
2. PayPal modal opens with calculated amount
3. User completes PayPal authentication
4. Payment processed through PayPal
5. Backend receives webhook confirmation
6. Off-chain record created
7. NFT minting triggered via admin wallet
8. User receives certificate via email/download

**Security**:
- PayPal transaction IDs stored on-chain
- Duplicate prevention via transaction hash
- Admin multi-sig for NFT minting
- Audit trail in smart contract events

### Price Oracle Integration

**Purpose**: Real-time carbon credit pricing

**Implementation**:
```typescript
interface PriceOracle {
  getLatestPrice(): Promise<number>;  // USD per ton CO2
  getHistoricalPrice(timestamp: number): Promise<number>;
  getPriceWithConfidence(): Promise<{ price: number; confidence: number }>;
}
```

**Data Sources**:
- Chainlink price feeds (primary)
- Fallback APIs (secondary)
- Manual override (emergency)

**Update Frequency**: Every 15 minutes

**Price Range**: $12-25 per ton CO₂ (Indonesian market rates)

---

## Storage Strategy

### Three-Tier Storage Architecture

#### 1. Client-Side Storage (LocalStorage)

**Use Cases**:
- Historical carbon data (last 12 months)
- User preferences and settings
- Draft carbon records
- Chart display configurations

**Data Structure**:
```typescript
interface LocalStorageSchema {
  carbonHistory: CarbonRecord[];
  userPreferences: UserSettings;
  draftRecords: DraftRecord[];
  lastSync: number;
}
```

**Advantages**:
- Instant access
- No network latency
- Offline capability
- Zero cost

**Limitations**:
- 5-10 MB limit
- Not synchronized across devices
- No backup/recovery

#### 2. Decentralized Storage (IPFS)

**Use Cases**:
- Carbon data snapshots
- NFT metadata
- Certificate documents
- Audit reports

**Content Types**:
- Carbon records (JSON)
- Achievement metadata (JSON)
- PDF certificates
- Image assets

**IPFS Integration**:
```typescript
async function uploadToIPFS(data: object): Promise<string> {
  const blob = new Blob([JSON.stringify(data)], { type: 'application/json' });
  const formData = new FormData();
  formData.append('file', blob);
  
  const response = await fetch('https://api.pinata.cloud/pinning/pinFileToIPFS', {
    method: 'POST',
    headers: { Authorization: `Bearer ${PINATA_JWT}` },
    body: formData
  });
  
  const { IpfsHash } = await response.json();
  return IpfsHash;
}
```

**Advantages**:
- Permanent storage
- Content-addressed
- Censorship-resistant
- Verifiable integrity

**Gateway**: Pinata for reliability and speed

#### 3. Blockchain Storage (Ethereum)

**Use Cases**:
- Carbon record hashes
- Ownership records
- Transaction history
- Governance proposals

**Data Optimization**:
- Store hashes, not full data
- Use events for historical queries
- Batch operations where possible
- Compress data before storage

**Gas Optimization**:
```solidity
// Efficient storage pattern
mapping(uint256 => bytes32) public recordHashes;  // 32 bytes
mapping(address => uint256[]) public userRecords; // Array of IDs

// Instead of:
mapping(uint256 => CarbonRecord) public records;  // Full struct
```

---

## Security Considerations

### Smart Contract Security

**Audit Checklist**:
- ✅ Reentrancy protection (ReentrancyGuard)
- ✅ Access control (Ownable, Role-based)
- ✅ Integer overflow protection (Solidity 0.8+)
- ✅ Front-running mitigation
- ✅ Gas limit considerations

**Security Patterns**:

**Access Control**:
```solidity
modifier onlyOwner() {
    require(msg.sender == owner, "Unauthorized");
    _;
}

modifier validRecord(uint256 recordId) {
    require(recordId > 0 && recordId <= totalRecords, "Invalid record");
    _;
}
```

**Reentrancy Protection**:
```solidity
bool private locked;

modifier noReentrancy() {
    require(!locked, "Reentrant call");
    locked = true;
    _;
    locked = false;
}
```

**Input Validation**:
```solidity
function storeRecord(string memory dataHash, uint256 carbonValue) external {
    require(bytes(dataHash).length > 0, "Empty hash");
    require(carbonValue > 0, "Invalid carbon value");
    require(carbonValue < 1e12, "Value too large");
    // ... rest of function
}
```

### Frontend Security

**Authentication**:
- SIWE (Sign-In With Ethereum) for wallet authentication
- Message signing instead of private key exposure
- Nonce generation for replay attack prevention

**Data Validation**:
- CSV/JSON schema validation
- File size limits (max 10 MB)
- Sanitization of user inputs
- Type checking with TypeScript

**Web3 Security**:
```typescript
// Safe contract interaction
try {
    const tx = await contract.method(...args);
    const receipt = await tx.wait();
    
    if (receipt.status === 1) {
        // Success
    } else {
        // Failed
    }
} catch (error) {
    // Handle error (user rejection, insufficient funds, etc.)
    console.error('Transaction failed:', error);
}
```

**XSS Prevention**:
- React's automatic escaping
- No `dangerouslySetInnerHTML`
- Content Security Policy headers
- Sanitized user inputs

### Payment Security

**Crypto Payments**:
- Smart contract amount verification
- Gas price limits
- Transaction confirmation requirements
- Event-based verification

**PayPal Integration**:
- Server-side verification
- Webhook signature validation
- Transaction ID uniqueness
- Amount matching verification

---

## Scalability & Performance

### Frontend Optimization

**Code Splitting**:
```typescript
// Dynamic imports for heavy components
const CarbonAnalysis = dynamic(() => import('@/components/CarbonAnalysis'), {
    loading: () => <LoadingSpinner />,
    ssr: false
});
```

**Image Optimization**:
- Next.js Image component
- WebP format with fallbacks
- Lazy loading
- Responsive images

**Bundle Size**:
- Tree shaking enabled
- Dynamic imports for route-based splitting
- External library minimization
- Compression (gzip/brotli)

### Blockchain Scalability

**Gas Optimization**:
- Batch operations where possible
- Efficient data structures
- Event-based data retrieval
- Minimal on-chain storage

**Transaction Batching**:
```typescript
// Batch multiple records in single transaction
async function batchStoreRecords(records: CarbonRecord[]): Promise<void> {
    const hashes = records.map(r => r.dataHash);
    const values = records.map(r => r.carbonValue);
    
    const tx = await contract.batchStore(hashes, values);
    await tx.wait();
}
```

**Future Scaling Solutions**:
- Layer 2 deployment (Base, Optimism)
- State channels for frequent updates
- Rollup technology for data availability
- Cross-chain bridges for multi-chain support

### Data Processing

**Large Dataset Handling**:
```typescript
// Chunked processing for large CSV files
function processLargeFile(file: File): Promise<ProcessedData[]> {
    return new Promise((resolve) => {
        const results: ProcessedData[] = [];
        
        Papa.parse(file, {
            chunk: (chunk) => {
                // Process chunk
                results.push(...processChunk(chunk.data));
            },
            complete: () => resolve(results)
        });
    });
}
```

**Caching Strategy**:
- Carbon calculations cached in LocalStorage
- Blockchain data cached with TTL
- Chart data memoized
- API responses cached (5 min TTL)

---

## Integration Patterns

### Cloud Provider Integration

**Architecture**: Simulated pull with extensible design

**Supported Providers**:
- Amazon Web Services (AWS CloudWatch)
- Google Cloud Platform (GCP Monitoring)
- Microsoft Azure (Azure Monitor)

**Integration Pattern**:
```typescript
interface CloudProvider {
    authenticate(credentials: Credentials): Promise<boolean>;
    fetchMetrics(dateRange: DateRange): Promise<Metric[]>;
    validateConnection(): Promise<boolean>;
}

class AWSProvider implements CloudProvider {
    async authenticate(credentials: AWSCredentials): Promise<boolean> {
        // AWS authentication logic
    }
    
    async fetchMetrics(dateRange: DateRange): Promise<Metric[]> {
        // CloudWatch API calls
    }
}
```

**Future Implementation**:
- OAuth 2.0 authentication
- Real-time metric streaming
- Multi-region support
- Cost optimization APIs

### Third-Party APIs

**Current Integrations**:
1. **PayPal**: Payment processing
2. **Pinata**: IPFS pinning service
3. **Infura**: Ethereum RPC provider
4. **Chainlink**: Price oracle (planned)

**API Client Pattern**:
```typescript
class APIClient {
    private baseURL: string;
    private apiKey: string;
    
    async request<T>(endpoint: string, options?: RequestOptions): Promise<T> {
        const response = await fetch(`${this.baseURL}${endpoint}`, {
            ...options,
            headers: {
                'Authorization': `Bearer ${this.apiKey}`,
                'Content-Type': 'application/json',
                ...options?.headers
            }
        });
        
        if (!response.ok) throw new Error(`API error: ${response.status}`);
        return response.json();
    }
}
```

### Export System

**Supported Formats**:
- PDF (jsPDF)
- CSV (Papa Parse)
- Excel (xlsx/SheetJS)
- JSON (native)

**Export Architecture**:
```typescript
interface ExportStrategy {
    export(data: CarbonData): Promise<Blob>;
}

class PDFExportStrategy implements ExportStrategy {
    async export(data: CarbonData): Promise<Blob> {
        const doc = new jsPDF();
        // PDF generation logic
        return doc.output('blob');
    }
}

class ExportManager {
    private strategies: Map<string, ExportStrategy>;
    
    export(format: string, data: CarbonData): Promise<Blob> {
        const strategy = this.strategies.get(format);
        if (!strategy) throw new Error(`Unsupported format: ${format}`);
        return strategy.export(data);
    }
}
```

---

## Deployment Architecture

### Production Environment

**Hosting**: Vercel Edge Network
- Global CDN distribution
- Automatic HTTPS
- Serverless functions
- Edge caching

**Build Configuration**:
```javascript
// next.config.js
module.exports = {
    output: 'standalone',
    images: {
        domains: ['ipfs.io', 'gateway.pinata.cloud']
    },
    webpack: (config) => {
        config.resolve.fallback = { fs: false, net: false };
        return config;
    }
}
```

**Environment Variables**:
```bash
NEXT_PUBLIC_INFURA_RPC=https://sepolia.infura.io/v3/...
NEXT_PUBLIC_CONTRACT_ADDRESS=0x874378E56D92a0C4633b27A1730AD0CF8e7b4891
NEXT_PUBLIC_PINATA_JWT=...
PAYPAL_CLIENT_ID=...
PAYPAL_SECRET=...
```

### CI/CD Pipeline

**GitHub Actions Workflow**:
```yaml
name: Deploy to Production

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
      - run: npm ci
      - run: npm run build
      - run: npm run test
      - uses: vercel/actions/deploy@v1
```

**Quality Gates**:
- TypeScript compilation
- ESLint validation
- Unit tests
- Build verification
- Lighthouse performance audit

---

## Monitoring & Observability

### Metrics Tracking

**Frontend Metrics**:
- Page load time
- Time to interactive
- Core Web Vitals
- Error rates
- User actions

**Blockchain Metrics**:
- Transaction success rate
- Average gas cost
- Block confirmation time
- Contract call latency
- Failed transaction analysis

**Business Metrics**:
- Total carbon recorded
- Offset purchases
- NFT minting rate
- User engagement
- Conversion funnel

### Error Handling

**Frontend Error Boundary**:
```typescript
class ErrorBoundary extends React.Component {
    componentDidCatch(error: Error, errorInfo: ErrorInfo) {
        // Log error to monitoring service
        console.error('Error caught:', error, errorInfo);
    }
    
    render() {
        if (this.state.hasError) {
            return <ErrorFallback />;
        }
        return this.props.children;
    }
}
```

**Blockchain Error Handling**:
```typescript
try {
    const tx = await contract.method(...args);
    await tx.wait();
} catch (error) {
    if (error.code === 'ACTION_REJECTED') {
        // User rejected transaction
    } else if (error.code === 'INSUFFICIENT_FUNDS') {
        // Not enough ETH
    } else {
        // Other error
    }
}
```

---

## Future Architecture Considerations

### Phase 4: Enterprise Features

**Database Layer**:
- PostgreSQL for relational data
- Redis for caching
- TimescaleDB for time-series data

**Backend API**:
- RESTful API (Express.js)
- GraphQL for complex queries
- WebSocket for real-time updates

**Authentication**:
- JWT-based auth
- OAuth 2.0 integration
- Role-based access control (RBAC)

### Phase 5: Scale & Optimization

**Mainnet Deployment**:
- Base blockchain (Optimistic L2)
- Lower gas costs
- Faster finality
- Better UX

**Advanced Analytics**:
- Real ML models for predictions
- Big data processing (Apache Spark)
- Real-time streaming (Apache Kafka)
- Advanced visualization (D3.js)

**Mobile Application**:
- React Native for iOS/Android
- Shared codebase with web
- Mobile-optimized UX
- Native Web3 wallet integration

---

## Conclusion

RANTAI 3C's architecture is designed with modularity, security, and scalability at its core. The hybrid approach balances decentralization benefits with practical usability, enabling both crypto-native users and traditional payment users to participate in carbon management.

The smart contract suite provides immutable record-keeping, while IPFS ensures data permanence. The dual payment system removes barriers to entry, making carbon offsetting accessible to a broader audience.

Future architectural improvements will focus on scaling to enterprise usage, deploying to Layer 2 networks, and implementing real-time ML analytics for predictive carbon management.

---

**Architecture Version**: 1.0  
**Last Updated**: January 2024  
**Status**: Production-Ready  
**Network**: Ethereum Sepolia Testnet  
**Framework**: Next.js 14 + TypeScript  

For technical questions or architecture discussions, refer to the [GitHub repository](https://github.com/mrbrightsides/3c) or contact the development team.
