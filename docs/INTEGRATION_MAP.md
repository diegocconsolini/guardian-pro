# 🔗 Guardian Pro - Integration Map

## System Integration Overview

This document defines how all Guardian Pro components integrate and communicate with each other.

---

## 🏗️ Core Integration Architecture

### Central Communication Hub
```
                    ┌─────────────────┐
                    │   Event Bus     │
                    │  (Redis/NATS)   │
                    └─────────────────┘
                            │
        ┌───────────────────┼───────────────────┐
        │                   │                   │
   ┌────▼────┐         ┌────▼────┐         ┌────▼────┐
   │   API   │         │   AI    │         │Detection│
   │ Gateway │         │ Engine  │         │ Modules │
   └─────────┘         └─────────┘         └─────────┘
        │                   │                   │
   ┌────▼────┐         ┌────▼────┐         ┌────▼────┐
   │Frontend │         │Database │         │Response │
   │   UI    │         │  Layer  │         │ Engine  │
   └─────────┘         └─────────┘         └─────────┘
```

---

## 📡 Communication Patterns

### 1. Event-Driven Architecture
**Pattern**: Publish/Subscribe via Event Bus  
**Use Case**: Real-time system communication  
**Components**: All system components

```python
# Event Interface Definition
@dataclass
class GuardianEvent:
    event_id: str
    event_type: str
    timestamp: datetime
    source_component: str
    data: Dict[str, Any]
    correlation_id: Optional[str] = None
    priority: int = 5  # 1=highest, 10=lowest
```

### 2. Request/Response Pattern
**Pattern**: Synchronous API calls  
**Use Case**: Direct data queries and commands  
**Components**: Frontend ↔ API Gateway, API ↔ AI Engine

```python
# API Interface Definition
@dataclass
class APIRequest:
    endpoint: str
    method: str  # GET, POST, PUT, DELETE
    headers: Dict[str, str]
    payload: Optional[Dict[str, Any]] = None
    auth_token: Optional[str] = None

@dataclass
class APIResponse:
    status_code: int
    data: Optional[Dict[str, Any]]
    error: Optional[str] = None
    response_time_ms: float
```

### 3. Stream Processing
**Pattern**: Continuous data streams  
**Use Case**: Real-time threat detection  
**Components**: Detection Modules → Event Bus → Response Engine

```python
# Stream Interface Definition
@dataclass
class ThreatStream:
    stream_id: str
    data_type: str  # 'network', 'system', 'application'
    batch_size: int
    processing_interval_ms: int
    data: Iterator[Dict[str, Any]]
```

---

## 🔌 Integration Contracts

### Event Bus Integration

#### Event Types
```python
class EventTypes:
    # Threat Events
    THREAT_DETECTED = "threat.detected"
    THREAT_UPDATED = "threat.updated"
    THREAT_RESOLVED = "threat.resolved"
    
    # System Events
    SYSTEM_HEALTH = "system.health"
    COMPONENT_STATUS = "component.status"
    PERFORMANCE_METRIC = "performance.metric"
    
    # Response Events
    ACTION_REQUESTED = "response.action_requested"
    ACTION_EXECUTED = "response.action_executed"
    ACTION_FAILED = "response.action_failed"
    
    # AI Events
    MODEL_INFERENCE = "ai.inference"
    MODEL_RETRAINED = "ai.model_retrained"
    ANOMALY_DETECTED = "ai.anomaly_detected"
    
    # Configuration Events
    CONFIG_UPDATED = "config.updated"
    RULE_CHANGED = "config.rule_changed"
```

#### Event Publisher Interface
```python
class EventPublisher:
    async def publish(self, event: GuardianEvent) -> bool
    async def publish_batch(self, events: List[GuardianEvent]) -> int
    async def schedule_event(self, event: GuardianEvent, delay_seconds: int) -> str
```

#### Event Subscriber Interface
```python
class EventSubscriber:
    async def subscribe(self, event_types: List[str], callback: Callable) -> str
    async def unsubscribe(self, subscription_id: str) -> bool
    async def get_subscription_status(self, subscription_id: str) -> Dict[str, Any]
```

---

## 🛠️ Component Integration Specifications

### 1. API Gateway ↔ Frontend Integration

#### WebSocket Connection
```typescript
// Frontend WebSocket Interface
interface WebSocketClient {
  connect(url: string, token: string): Promise<void>;
  subscribe(eventType: string, callback: Function): string;
  unsubscribe(subscriptionId: string): void;
  send(eventType: string, data: any): void;
  disconnect(): void;
}

// Message Format
interface WebSocketMessage {
  type: 'event' | 'response' | 'error';
  eventType?: string;
  requestId?: string;
  data: any;
  timestamp: string;
}
```

#### REST API Endpoints
```python
# Core API Endpoints
class APIEndpoints:
    # Authentication
    POST_LOGIN = "/api/v1/auth/login"
    POST_LOGOUT = "/api/v1/auth/logout"
    POST_REFRESH = "/api/v1/auth/refresh"
    
    # Threats
    GET_THREATS = "/api/v1/threats"
    GET_THREAT_DETAIL = "/api/v1/threats/{threat_id}"
    POST_THREAT_ACTION = "/api/v1/threats/{threat_id}/actions"
    
    # System Health
    GET_HEALTH = "/api/v1/health"
    GET_METRICS = "/api/v1/metrics"
    GET_COMPONENTS = "/api/v1/components"
    
    # Configuration
    GET_CONFIG = "/api/v1/config"
    PUT_CONFIG = "/api/v1/config"
    GET_RULES = "/api/v1/rules"
    POST_RULE = "/api/v1/rules"
```

### 2. Detection Modules ↔ AI Engine Integration

#### Inference Request/Response
```python
@dataclass
class InferenceRequest:
    model_name: str
    input_data: np.ndarray
    request_id: str
    priority: int = 5
    timeout_ms: int = 5000
    
@dataclass
class InferenceResponse:
    request_id: str
    predictions: np.ndarray
    confidence_scores: List[float]
    processing_time_ms: float
    model_version: str
    error: Optional[str] = None
```

#### Model Management Interface
```python
class AIEngineInterface:
    async def load_model(self, model_name: str, model_path: str) -> bool
    async def unload_model(self, model_name: str) -> bool
    async def predict(self, request: InferenceRequest) -> InferenceResponse
    async def predict_batch(self, requests: List[InferenceRequest]) -> List[InferenceResponse]
    async def get_model_info(self, model_name: str) -> Dict[str, Any]
    async def health_check(self) -> Dict[str, Any]
```

### 3. Database Layer Integration

#### Data Access Interface
```python
class DatabaseInterface:
    # CRUD Operations
    async def create(self, table: str, data: Dict[str, Any]) -> str
    async def read(self, table: str, filters: Dict[str, Any]) -> List[Dict[str, Any]]
    async def update(self, table: str, id: str, data: Dict[str, Any]) -> bool
    async def delete(self, table: str, id: str) -> bool
    
    # Bulk Operations
    async def bulk_insert(self, table: str, data: List[Dict[str, Any]]) -> List[str]
    async def bulk_update(self, table: str, updates: List[Dict[str, Any]]) -> int
    
    # Queries
    async def query(self, sql: str, params: Dict[str, Any]) -> List[Dict[str, Any]]
    async def execute(self, sql: str, params: Dict[str, Any]) -> int
```

#### Data Models
```python
# Core Data Models
class ThreatModel:
    id: str
    threat_type: str
    severity: str
    source_ip: str
    target_ip: str
    timestamp: datetime
    status: str
    ai_confidence: float
    raw_data: Dict[str, Any]

class SystemMetricsModel:
    id: str
    component_name: str
    metric_name: str
    metric_value: float
    timestamp: datetime
    tags: Dict[str, str]

class ConfigurationModel:
    id: str
    component: str
    key: str
    value: Any
    updated_at: datetime
    updated_by: str
```

### 4. Response Engine Integration

#### Action Execution Interface
```python
@dataclass
class ResponseAction:
    action_id: str
    action_type: str  # 'block_ip', 'quarantine', 'alert', 'custom'
    parameters: Dict[str, Any]
    threat_id: str
    requester: str
    approval_required: bool = False
    
@dataclass
class ActionResult:
    action_id: str
    success: bool
    message: str
    execution_time_ms: float
    side_effects: List[str]
    rollback_possible: bool
```

#### Response Engine Interface
```python
class ResponseEngineInterface:
    async def execute_action(self, action: ResponseAction) -> ActionResult
    async def batch_execute(self, actions: List[ResponseAction]) -> List[ActionResult]
    async def rollback_action(self, action_id: str) -> ActionResult
    async def get_action_status(self, action_id: str) -> Dict[str, Any]
    async def list_available_actions(self) -> List[str]
```

---

## 🔄 Data Flow Patterns

### 1. Threat Detection Flow
```
Network Traffic → Detection Module → AI Engine → Event Bus → Response Engine
                       ↓                ↓            ↓           ↓
                   Database ←     Confidence  →  Frontend  →  Notification
```

### 2. Configuration Update Flow
```
Frontend → API Gateway → Config Service → Event Bus → All Components
    ↓           ↓             ↓              ↓           ↓
Database ←  Validation  →  Storage     →  Notification → Update
```

### 3. Real-time Dashboard Flow
```
All Components → Event Bus → WebSocket Gateway → Frontend
       ↓              ↓             ↓               ↓
   Metrics DB  →  Aggregation  →  Real-time  →  Dashboard
```

---

## 🔒 Security Integration

### Authentication Flow
```python
# JWT Token Structure
@dataclass
class JWTToken:
    user_id: str
    roles: List[str]
    permissions: List[str]
    issued_at: datetime
    expires_at: datetime
    session_id: str
```

### Authorization Interface
```python
class AuthorizationInterface:
    async def authenticate(self, credentials: Dict[str, str]) -> Optional[JWTToken]
    async def authorize(self, token: JWTToken, resource: str, action: str) -> bool
    async def refresh_token(self, refresh_token: str) -> Optional[JWTToken]
    async def revoke_token(self, token: str) -> bool
```

---

## 🚀 Performance Integration

### Caching Strategy
```python
class CacheInterface:
    async def get(self, key: str) -> Optional[Any]
    async def set(self, key: str, value: Any, ttl_seconds: int = 300) -> bool
    async def delete(self, key: str) -> bool
    async def invalidate_pattern(self, pattern: str) -> int
```

### Load Balancing
```python
class LoadBalancerInterface:
    async def register_service(self, service_name: str, endpoint: str) -> bool
    async def unregister_service(self, service_name: str, endpoint: str) -> bool
    async def get_service_endpoint(self, service_name: str) -> Optional[str]
    async def health_check_service(self, service_name: str) -> Dict[str, Any]
```

---

## 📊 Monitoring Integration

### Metrics Collection
```python
class MetricsInterface:
    def counter(self, name: str, value: int = 1, tags: Dict[str, str] = None)
    def gauge(self, name: str, value: float, tags: Dict[str, str] = None)
    def histogram(self, name: str, value: float, tags: Dict[str, str] = None)
    def timer(self, name: str) -> ContextManager
```

### Health Check Interface
```python
class HealthCheckInterface:
    async def check_component_health(self) -> Dict[str, Any]
    async def check_dependencies(self) -> Dict[str, Any]
    async def get_component_info(self) -> Dict[str, Any]
```

---

## 🔧 Development Integration

### Service Discovery
```python
class ServiceDiscoveryInterface:
    async def register_service(self, service_info: ServiceInfo) -> bool
    async def discover_service(self, service_name: str) -> List[ServiceInfo]
    async def watch_service(self, service_name: str, callback: Callable) -> str
    async def unwatch_service(self, watch_id: str) -> bool
```

### Configuration Management
```python
class ConfigurationInterface:
    async def get_config(self, component: str, key: str) -> Any
    async def set_config(self, component: str, key: str, value: Any) -> bool
    async def watch_config(self, component: str, callback: Callable) -> str
    async def get_all_config(self, component: str) -> Dict[str, Any]
```

---

## 📋 Integration Testing Strategy

### Contract Testing
- API contract validation using OpenAPI specs
- Event schema validation
- Database schema migrations
- Integration smoke tests

### End-to-End Testing
- Threat detection to response workflow
- Real-time dashboard updates
- Configuration change propagation
- Authentication and authorization flows

---

**Last Updated**: Session 1  
**Next Review**: Session 2  
**Integration Contracts Defined**: 12  
**Ready for Implementation**: Event Bus, API Gateway, Database Layer
