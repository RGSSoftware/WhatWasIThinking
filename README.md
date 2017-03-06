# What Was I Thinking  
As developers we’re constantly getting better at our craft, but before we become better we typically go through a learning period. Apart of this learning period we make mistakes that are not so obvious, but they will make you say “What was I thinking” months or days later.

This post is a way for me to review my older or recent work so I can learn, laugh, document and share my mistakes.  

### Equal, But Not Equal
```swift
struct Contact: Equatable {
    var id: String
    
    var name: String?
    var company: String?
    
    var phoneNumbers: [PhoneNumber] = []
    var postalAddresses: [PostalAddress] = []
    
    init() {
        id = UUID().uuidString
    }
    
    static func ==(lhs: Contact, rhs: Contact) -> Bool {
        return lhs.id == rhs.id
    }
}

struct PostalAddress: Equatable {
    var id: String
        
    var street: String?
    var city: String?
    var state: String?
    var zip: String?
    var country: String?
    
    var latitude: Double?
    var longitude: Double?
    
    init() {
        id = UUID().uuidString
    }
    
    static func ==(lhs: PostalAddress, rhs: PostalAddress) -> Bool {
        return lhs.id == rhs.id
    }
}

struct PhoneNumber: Equatable {
    var id: String
        
    var number: String?
    
    init() {
        id = UUID().uuidString
    }
    
    static func ==(lhs: PhoneNumber, rhs: PhoneNumber) -> Bool {
        return lhs.number == rhs.number
    }
}
```
If you're modeling your data equality with an id then this model gets the job done, but if you're not using an id then try using the struct's properties and a set for Contact's phoneNumbers and postalAddresses.

```swift
struct Contact: Equatable {
    var name: String?
    var company: String?
    
    var phoneNumbers = Set<PhoneNumber>()
    var postalAddresses = Set<PostalAddress>()
    
    static func ==(lhs: Contact, rhs: Contact) -> Bool {
        return lhs.name == rhs.name &&
            lhs.company == rhs.company &&
            lhs.phoneNumbers == rhs.phoneNumbers &&
            lhs.postalAddresses == rhs.postalAddresses
    }
}

struct PostalAddress: Hashable {
    var street: String?
    var city: String?
    var state: String?
    var zip: String?
    var country: String?
    
    var latitude: Double?
    var longitude: Double?
    
    var hashValue: Int {
        var hash = ""
        if let street = street {
            hash += street
        }
        if let city = city {
            hash += city
        }
        if let state = state {
            hash += state
        }
        if let zip = zip {
            hash += zip
        }
        if let latitude = latitude {
            hash += String(latitude)
        }
        if let longitude = longitude {
            hash += String(longitude)
        }
        
        return hash.hashValue 
    }
    
    static func ==(lhs: PostalAddress, rhs: PostalAddress) -> Bool {
        return lhs.street == rhs.street &&
            lhs.city == rhs.city &&
            lhs.state == rhs.state &&
            lhs.zip == rhs.zip &&
            lhs.country == rhs.country &&
            lhs.latitude == rhs.latitude &&
            lhs.longitude == rhs.longitude
    }
}

struct PhoneNumber: Hashable {
    var number: String?
    
    var hashValue: Int {
        return number?.hashValue ?? "".hashValue
    }
    
    static func ==(lhs: PhoneNumber, rhs: PhoneNumber) -> Bool {
        return lhs.number == rhs.number
    }
}
```
