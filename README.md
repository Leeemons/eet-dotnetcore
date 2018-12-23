
# EET
EET stands for Elektronická Evidence Tržeb, which is Czech version of Fiscal Printers.
It's an online API provided by the Ministry of Finance in a form of a SOAP Web Service.

## Key features
- No Czech abbreviations.
- Early data validation.
- Intuitive immutable DTOs.
- SOAP communication (including WS-Security signing).
- [PKP](doc/data.md) and [BKP](doc/data.md) security code computation.
- Support for parallel async requests.
- Logging support

## Known issues
- [8](https://github.com/MewsSystems/eet/issues/8): As the communication is done fully via HTTPS, we postponed the implementation of response signature verification. It's a potential security risk that will be addressed in upcoming releases.

## Usage
We tend to use immutable DTOs wherever possible, especially to ensure data validity.
We want the library to throw an error as soon as possible, i.e. when constructing corresponding data structures.
That is why we even introduce wrappers for simple datatypes.
Various usages are demonstrated in our test cases.

### Simplest usage example
```csharp
var certificate = new Certificate(
    password: "certificatePassword",
    data: certificateContentsByteArray
);

var record = new RevenueRecord(
    identification: new Identification(
        taxPayerIdentifier: new TaxIdentifier("CZ1234567890"),
        registryIdentifier: new RegistryIdentifier("01"),
        premisesIdentifier: new PremisesIdentifier(1),
        certificate: certificate
    ),
    revenue: new Revenue(
        gross: new CurrencyValue(1234.00m)
    ),
    billNumber: new BillNumber("2016-321")
);

var securityCode = record.SecurityCode; // BKP
var signature = record.Signature; // PKP

var client = new EetClient(certificate);
var response = await client.SendRevenueAsync(record);
if (response.IsSuccess)
{
    var fiscalCode = response.Success.FiscalCode;
}
```

- More examples are presented [here](doc/examples.md).
- Some of the data items are explained [here](doc/data.md).

# NuGet

This library is avalaible from NuGet Package Manager 

```
PM> Install-Package EET.NETCore
```

# Tests
Test are not currently running on Travis CI, as their network infrastructure is blocking HTTPS traffic directed into Europe, where the EET servers are located.

Otherwise, a xUnit test suite is a part of the library, it contains end-to-end tests that communicates with Playground EET servers to verify the lib is really working against the current EET version.

# Authors
Development: [@jirihelmich](https://github.com/jirihelmich)
Code review: [@siroky](https://github.com/siroky), [@onashackem](https://github.com/onashackem)

# Who uses the library in production?
- [Mews Systems](https://mewssystems.com) - Property Management Solution for the 21st century.
- [Hlídač EET](http://hlidaceet.cz) - Watchdog of the EET API endpoint and related services.
- Keszh, Neszh - [Hart](http://hartphp.com.pl/) Internal ERP System
- [Ankerdata EasiPOS](http://easipos.ankerdata.com/) - POS system
- [sySOft](http://www.sysoft.cz/) - POS system

We would like to hear your story and know who users of the lib are. Please, thank us for providing the library by sharing with us, who you are and letting us add you into this list.

The time to implement this was kindly provided by [Mews Systems](http://mewssystems.com).

# Donate
There is no need to donate the project, but thanks for considering it! Instead, if you like the project, star it here on GitHub :-)! Thanks!

If you still insist on donating, we accept gummy bears at Mews Systems s.r.o., Helénská 4, Praha 2, 120 00. This project was, of course, powered by a huge pile of gummy bears ;-)

# Related projects
- [eet-client](https://github.com/todvora/eet-client) by [@todvora](https://github.com/todvora): Java, MIT
- [SwiftEET](https://github.com/charlieMonroe/SwiftEET) by [@charlieMonroe](https://github.com/charlieMonroe): Swift, GPL-3.0

# Credits
- [Komodosoft](http://www.komodosoft.net) For publishing [the post](http://www.komodosoft.net/post/2016/03/24/sign-a-soap-message-using-x-509-certificate.aspx) about signing a SOAP message without using WCF.
- [@tomasdeml](https://github.com/tomasdeml): [PR#3](https://github.com/MewsSystems/eet/pull/3/files)
