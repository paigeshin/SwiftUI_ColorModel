# SwiftUI_ColorModel

### Reference

https://gist.github.com/klein-artur/025a0fa4f167a648d9ea

https://www.flux-academy.com/blog/what-is-color-theory

https://academy.realm.io/posts/architecting-a-robust-color-system-swift-tryswift-2017-ragone/

https://github.com/vicc/chameleon

https://stackoverflow.com/questions/37055755/computing-complementary-triadic-tetradic-and-analagous-colors

https://swdevnotes.com/swift/2022/define-colors-with-hue-saturation-and-brightness-rather-than-red-green-and-blue-properties/

https://www.swiftanytime.com/blog/whats-new-in-swiftui-colors-and-sf-symbols-ft-wwdc22

https://stackoverflow.com/questions/47091979/how-to-create-monochromatic-uiimage-on-ios


### Extension 

```swift
import SwiftUI

extension Color {
    
    func monochromaticColors(amount: Int) -> [Color] {
        UIColor(self).monochromaticColors(amount: amount).map{Color(uiColor: $0)}
    }
    
    func analogousColors(amount: Int) -> [Color] {
        UIColor(self).analogousColors(amount: amount).map{Color(uiColor: $0)}
    }
    
    func complementaryColors() -> [Color] {
        UIColor(self).complementaryColors().map{Color(uiColor: $0)}
    }
    
    func triadicColors() -> [Color] {
        UIColor(self).triadicColors().map{Color(uiColor: $0)}
    }
    
    
}

extension UIColor {
    func monochromaticColors(amount: Int) -> [UIColor] {
        var colors: [UIColor] = []
        let hue = hueComponent()
        let saturation = saturationComponent()
        let brightness = brightnessComponent()
        
        let step = 1.0 / CGFloat(amount + 1)
        
        for i in 1...amount {
            let newBrightness = brightness * CGFloat(i) * step
            let color = UIColor(hue: hue, saturation: saturation, brightness: newBrightness, alpha: 1.0)
            colors.append(color)
        }
        
        return colors.reversed()
    }
    
    func analogousColors(amount: Int) -> [UIColor] {
        var colors: [UIColor] = []
        let hue = hueComponent()
        let saturation = saturationComponent()
        let brightness = brightnessComponent()
        
        let step = 1.0 / CGFloat(amount + 1)
        
        for i in 1...amount {
            let newHue = (hue + (CGFloat(i) * step)).truncatingRemainder(dividingBy: 1.0)
            let color = UIColor(hue: newHue, saturation: saturation, brightness: brightness, alpha: 1.0)
            colors.append(color)
        }
        
        return colors
    }
    
    func complementaryColors() -> [UIColor] {
        let hue = hueComponent()
        let complementHue = (hue + 0.5).truncatingRemainder(dividingBy: 1.0)
        let saturation = saturationComponent()
        let brightness = brightnessComponent()
        
        let color1 = UIColor(hue: hue, saturation: saturation, brightness: brightness, alpha: 1.0)
        let color2 = UIColor(hue: complementHue, saturation: saturation, brightness: brightness, alpha: 1.0)
        
        return [color1, color2]
    }
    
    func triadicColors() -> [UIColor] {
        let hue = hueComponent()
        let triadicHue1 = (hue + 1.0/3.0).truncatingRemainder(dividingBy: 1.0)
        let triadicHue2 = (hue + 2.0/3.0).truncatingRemainder(dividingBy: 1.0)
        let saturation = saturationComponent()
        let brightness = brightnessComponent()
        
        let color1 = UIColor(hue: hue, saturation: saturation, brightness: brightness, alpha: 1.0)
        let color2 = UIColor(hue: triadicHue1, saturation: saturation, brightness: brightness, alpha: 1.0)
        let color3 = UIColor(hue: triadicHue2, saturation: saturation, brightness: brightness, alpha: 1.0)
        
        return [color1, color2, color3]
    }
    
    private func hueComponent() -> CGFloat {
        var hue: CGFloat = 0.0
        getHue(&hue, saturation: nil, brightness: nil, alpha: nil)
        return hue
    }
    
    private func saturationComponent() -> CGFloat {
        var saturation: CGFloat = 0.0
        getHue(nil, saturation: &saturation, brightness: nil, alpha: nil)
        return saturation
    }
    
    private func brightnessComponent() -> CGFloat {
        var brightness: CGFloat = 0.0
        getHue(nil, saturation: nil, brightness: &brightness, alpha: nil)
        return brightness
    }
}

```
