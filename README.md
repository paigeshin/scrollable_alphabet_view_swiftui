# scrollable_alphabet_view_swiftui

```swift

import SwiftUI

struct ContentView: View {
    
    var data: [String: [String]] = [:]
    let alphabet: String = "ABCDEFGHIJKLMNOPQRSTUVWXYZ"
    
    init() {
        UIFont.familyNames.forEach { fontName in
            for character in alphabet {
                if fontName == "Bodoni Ornaments" {
                    continue
                }
                if fontName.first == character {
                    if data.keys.contains(String(character)) {
                        data[String(character)]?.append(fontName)
                    } else {
                        data[String(character)] = [fontName]
                    }
                }
            }
        }
    }
    
    
    var body: some View {
        ScrollViewReader { proxy in
            ScrollView {
                LazyVStack {
                    ForEach(data.sorted(by: { (lhs, rhs) -> Bool in
                        lhs.key < rhs.key
                    }), id: \.key) { categoryName, devicesArray in
                        Section(
                            header: Text(categoryName)
                                .font(.title)
                                .fontWeight(.bold)
                                .padding()
                                .frame(maxWidth: .infinity, alignment: .leading)
                        ) {
                            ForEach(devicesArray, id: \.self) { name in
                                Text(name)
                                    .padding()
                                    .frame(maxWidth: .infinity, alignment: .leading)
                            }
                        }
                    }
                }
            }
            .overlay(
                SectionIndexTitles(proxy: proxy, titles: data.keys.sorted())
                    .frame(maxWidth: .infinity, alignment: .trailing)
                    .padding()
            )
        }
        .navigationBarTitle("Apple Devices")
    }
    

    
}

struct SectionIndexTitles: View {
    let proxy: ScrollViewProxy
    let titles: [String]
    @GestureState private var dragLocation: CGPoint = .zero
    
    var body: some View {
        VStack {
            ForEach(titles, id: \.self) { title in
                Text(title)
                    .font(.custom(title, size: 8))
                    .background(dragObserver(title: title))
            }
        }
        .gesture(
            DragGesture(minimumDistance: 0, coordinateSpace: .global)
                .updating($dragLocation) { value, state, _ in
                    state = value.location
                }
        )
    }
    
    func dragObserver(title: String) -> some View {
        GeometryReader { geometry in
            dragObserver(geometry: geometry, title: title)
        }
    }
    
    func dragObserver(geometry: GeometryProxy, title: String) -> some View {
        if geometry.frame(in: .global).contains(dragLocation) {
            DispatchQueue.main.async {
                proxy.scrollTo(title, anchor: .center)
            }
        }
        return Rectangle().fill(Color.clear)
    }
    
}



```
