---
publish: true
---
```swift

struct Example: View {
    @State private var isPresented = false

    var body: some View {
        Text("Hello, World!")
            .onTapGesture {
                self.isPresented.toggle()
            }
            .sheet(isPresented: $isPresented) {
                Text("Sheet")
            }
    }

    func fibonacci(_ n: Int) -> Int {
        if n <= 1 {
            return n
        }
        return fibonacci(n - 1) + fibonacci(n - 2)
    }
}

```