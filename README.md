# That
[POC] Stupid DI

```swift
class That {
    public enum Scope {
        case global
        case local(scope: String)
        case shared
        
        var url: String {
            switch self {
            case .global:
                return "global"
            case .shared:
                return "shared"
            case .local(let scope):
                return "local-\(scope)"
            }
        }
    }
    
    public var values: [String: Any] = [:]
    
    public func capture(for scope: Scope = .global,
                        value: Any) -> Any {
        values["\(type(of: value))-\(scope.url)"] = value
        
        return value
    }
    
    public func resolve<T>(type: T.Type? = nil,
                           for scope: Scope = .global) -> T {
        guard let type = type else {
            return values["\(T.self)-\(scope.url)"] as! T
        }
        
        return values["\(type)-\(scope.url)"] as! T
    }
}

// ...

struct Model {
    let name: String
    let age: Int
}

let that = That()

let model = Model(name: "J Doe", age: 25)
let differentModel = Model(name: "M Ohe", age: 32)


that.capture(value: model) // (Scope: .global)
that.capture(for: .shared, value: differentModel)

let resolvedModel: Model = that.resolve()
print(resolvedModel) // (Scope: .global) Model(name: "J Doe", age: 25) 
```
