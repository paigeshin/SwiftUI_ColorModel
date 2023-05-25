# SwiftUI_ColorModel

### Color Model 

```swift
struct ColorModel {
    var hueDegrees: Double
    private var sat: Double
    private var bright: Double

    let totalDegrees = 360.0
    
    init(hueDegrees: Double, sat: Double, bright: Double) {
        self.hueDegrees = hueDegrees
        self.sat = sat
        self.bright = bright
    }
    
    init() {
        self.init(hueDegrees: 0, sat: 1.0, bright: 1.0)
    }
    
    var hueDouble: Double {
        return Double(self.hueDegrees) / totalDegrees
    }

    var color: Color {
        return Color(hue: hueDouble, saturation: sat, brightness: bright)
    }
    
    // Monochromatic
    var monochromaticColors: [Color] {
        return [
            Color(hue: hueDouble, saturation: sat, brightness: bright),
            Color(hue: hueDouble, saturation: (sat * 0.8), brightness: bright),
            Color(hue: hueDouble, saturation: (sat * 0.6), brightness: bright),
            Color(hue: hueDouble, saturation: (sat * 0.4), brightness: bright)
        ]
    }
    
    private func adjustHue(_ value: Double, percent adjustment: Double) -> Double {
        return Double((Int((value * 100) + adjustment)) % 100) / 100.0
    }
    
    // Analogous
    var analogousColors: [Color] {
        let hue1 = adjustHue(hueDouble, percent: 4)
        let hue2 = adjustHue(hueDouble, percent: -4)
        return [
            Color(hue: hueDouble, saturation: sat, brightness: bright),
            Color(hue: hue1, saturation: sat, brightness: bright),
            Color(hue: hue2, saturation: sat, brightness: bright)
        ]
    }

    // Complementary
    var complementaryColors: [Color] {
        let hue1 = adjustHue(hueDouble, percent: 50)
        return [
            Color(hue: hueDouble, saturation: sat, brightness: bright),
            Color(hue: hue1, saturation: sat, brightness: bright)
        ]
    }
    
    // Triadic
    var triadicColors: [Color] {
        let hue1 = adjustHue(hueDouble, percent: 33.33)
        let hue2 = adjustHue(hueDouble, percent: 66.66)
        return [
            Color(hue: hueDouble, saturation: sat, brightness: bright),
            Color(hue: hue1, saturation: sat, brightness: bright),
            Color(hue: hue2, saturation: sat, brightness: bright)
        ]
    }
}
```

### Color Model Extension 

```swift
struct ColorModel {
    var hueDegrees: Double
    private var sat: Double
    private var bright: Double
    
    let totalDegrees = 360.0
    
    init(hueDegrees: Double, sat: Double, bright: Double) {
        self.hueDegrees = hueDegrees
        self.sat = sat
        self.bright = bright
    }
    
    init() {
        self.init(hueDegrees: 0, sat: 1.0, bright: 1.0)
    }
    
    var hueDouble: Double {
        return Double(self.hueDegrees) / totalDegrees
    }
    
    var color: Color {
        return Color(hue: hueDouble, saturation: sat, brightness: bright)
    }
    
    // Monochromatic
    var monochromaticColors: [Color] {
        return [
            Color(hue: hueDouble, saturation: sat, brightness: bright),
            Color(hue: hueDouble, saturation: (sat * 0.8), brightness: bright),
            Color(hue: hueDouble, saturation: (sat * 0.6), brightness: bright),
            Color(hue: hueDouble, saturation: (sat * 0.4), brightness: bright)
        ]
    }
    
    private func adjustHue(_ value: Double, percent adjustment: Double) -> Double {
        return Double((Int((value * 100) + adjustment)) % 100) / 100.0
    }
    
    // Analogous
    var analogousColors: [Color] {
        let hue1 = adjustHue(hueDouble, percent: 4)
        let hue2 = adjustHue(hueDouble, percent: -4)
        return [
            Color(hue: hueDouble, saturation: sat, brightness: bright),
            Color(hue: hue1, saturation: sat, brightness: bright),
            Color(hue: hue2, saturation: sat, brightness: bright)
        ]
    }
    
    // Complementary
    var complementaryColors: [Color] {
        let hue1 = adjustHue(hueDouble, percent: 50)
        return [
            Color(hue: hueDouble, saturation: sat, brightness: bright),
            Color(hue: hue1, saturation: sat, brightness: bright)
        ]
    }
    
    // Triadic
    var triadicColors: [Color] {
        let hue1 = adjustHue(hueDouble, percent: 33.33)
        let hue2 = adjustHue(hueDouble, percent: 66.66)
        return [
            Color(hue: hueDouble, saturation: sat, brightness: bright),
            Color(hue: hue1, saturation: sat, brightness: bright),
            Color(hue: hue2, saturation: sat, brightness: bright)
        ]
    }
}

extension Color {
    
    var colorModel: ColorModel {
        let hue = self.rgbToHue(r: self.components.red, g: self.components.blue, b: self.components.blue)
        return ColorModel(hueDegrees: hue.h,
                          sat: hue.s,
                          bright: hue.b)
    }
    
    private var components: (red: CGFloat, green: CGFloat, blue: CGFloat, opacity: CGFloat) {
        
#if canImport(UIKit)
        typealias NativeColor = UIColor
#elseif canImport(AppKit)
        typealias NativeColor = NSColor
#endif
        
        var r: CGFloat = 0
        var g: CGFloat = 0
        var b: CGFloat = 0
        var o: CGFloat = 0
        
        guard NativeColor(self).getRed(&r, green: &g, blue: &b, alpha: &o) else {
            // You can handle the failure here as you want
            return (0, 0, 0, 0)
        }
        
        return (r, g, b, o)
    }
    
    
    private func rgbToHue(r:CGFloat, g:CGFloat, b:CGFloat) -> (h:CGFloat, s:CGFloat, b:CGFloat) {
        let minV:CGFloat = CGFloat(min(r, g, b))
        let maxV:CGFloat = CGFloat(max(r, g, b))
        let delta:CGFloat = maxV - minV
        var hue:CGFloat = 0
        if delta != 0 {
            if r == maxV {
                hue = (g - b) / delta
            }
            else if g == maxV {
                hue = 2 + (b - r) / delta
            }
            else {
                hue = 4 + (r - g) / delta
            }
            hue *= 60
            if hue < 0 {
                hue += 360
            }
        }
        let saturation = maxV == 0 ? 0 : (delta / maxV)
        let brightness = maxV
        return (h:hue/360, s:saturation, b:brightness)
    }
    
    
}

```
