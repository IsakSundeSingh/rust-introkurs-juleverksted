# Introkurs til Rust

Kurset består av to deler:

1. En introduksjon til Rust via en presentasjon, som ligger på `gh-pages`-branchen på dette repositoryet, generert fra [`Intro.md`](./Intro.md) ved bruk av [Marp](https://marp.app/).
2. Denne workshopen, som kan følges ved å fortsette å lese denne teksten.

Gå gjennom presentasjonen først eller hopp rett på workshopen om du kan litt Rust fra før 😉

# Workshop

_Emojis_ 😺

Du vil se noen emojsi gjennom workshopen. Disse betyr ca dette:

🏆 Oppgave: Oppgaven du skal gjøre

💡 Tips: Ekstra tips/hint

🚨 Løsningsforslag: Et løsningsforslag for oppgaven, men bare et forslag

## Del 1 - Gjettespill

Vi skal lage et kort lite gjettespill, altså at programmet finner frem et tall, som brukeren skal gjette seg frem til. Tallet programmet lager er et tall i [1,100], og brukeren får vite om gjettet var for lavt, høyt eller rett på etter hvert gjett. Spillet avsluttes når spilleren gjetter rett. Det hemmelige tallet må genereres tilfeldig ved oppstart.

---

### 0. Lag prosjektet

Vi kan ikke gjøre noe uten å ha et prosjekt.

🏆 Lag prosjektet ditt med `cargo new` og gi det et navn

<details>
<summary>🚨 Løsningsforslag</summary>

Bruk `cargo new` og navnet på spillet ditt så du kjører:

```shell
> cargo new guessing-game
```

</details>

---

### 1. Hardkod tall, ta i mot input

Vi starter enkelt. Hardkod et heltall som spillet viser for brukeren, og heller fokuser på å ta imot input fra brukeren.

🏆 Ta i mot et input fra brukeren og print ut igjen at brukeren gjettet det inputet.

💡 Tips: [`std::io::stdin`](https://doc.rust-lang.org/stable/std/io/fn.stdin.html) er en funksjon i standardbiblioteket for å interagere med terminalinput.
💡 Supertips: I lenken over er det et eksempel som viser ca. nøyaktig hva du må gjøre.
💡 Tips: Husk at du kan kjøre `cargo run` for å kompilere og kjøre koden din

<details>
<summary>🚨 Løsningsforslag</summary>

Inni `src/main.rs` endrer du `fn main() {}` til:

```rust
fn main() {
    let secret_number = 42;
    let mut input = String::new();

    println!("I'm thinking of a number between 1 and 100. Please input your guess");
    std::io::stdin().read_line(&mut input).expect("This should work, otherwise fail with this error message");
    println!("You guessed {input}!");
}
```

</details>

---

### 2. Sjekk om det er riktig gjett

Litt kjedelig å bare spytte ut igjen det brukeren spytta inn, så la oss sjekke om tallet matcher vårt hemmelige tall.

🏆 Sjekk om inputet er nøyaktig likt tallet programmet ditt tenker på og print ut om det er et vinn eller tap.

💡 Tips: Kan man egentlig sammenligne en string med en int?

<details>
<summary>🚨 Løsningsforslag</summary>

Endre delen av main-funksjonen som printer ut igjen inputet til følgende. Et tall kan ikke direkte sammenlignes med en string, så vi konverterer tallet til en string først:

```rust
if input == secret_number.to_string() {
    println!("You guessed {input}, which is the secret number. Congratulations, you win!");
} else {
    println!("You guessed {input}, which is incorrect. You lose :(");
}
```

</details>

---

### 3. Konverter tallet til et heltall

Foreløpig sjekker vi bare om inputet er nøyaktig lik det hemmelige tallet. For å kunne sammenligne inputet med tallet vårt og finne ut hvilket som er størst bør vi kanskje konvertere det til et heltall?

🏆 _Parse_ inputet til et tall og bruk `unwrap` eller `expect` på parse-resultatet med en feilmelding som sier til spilleren at de må skrive et tall dersom det feiler

💡 Å [_parse_](https://doc.rust-lang.org/stable/std/primitive.str.html#method.parse) et tall er kanskje enklere enn du tror?

<details>
<summary>🚨 Løsningsforslag</summary>

Kall `.parse`-metoden på inputet for å parse det til et heltall. Sørg for å lagre verdien i en variabel med en type som sier hva resultatet skal bli. Da vil kompilatoren skjønne hvilken type du prøver å parse til. Kall `.expect("Please input a number")` på resultatet fra parsingen for at en feilmelding gis til spilleren om de skriver inn et ikke-tall.

```rust
let parsed_input: u32 = input.parse().expect("Please input a number");
```

Endre sammenligningen av inputet til å bruke `parsed_input`.

</details>

---

### 4. Sjekk hvilket tall som er størst

Nå kan vi faktisk bruke tallet og finne ut ikke bare om inputet er riktig gjett, men om det er større eller mindre enn vårt hemmelige tall, og guide spilleren på rett vei.

🏆 Bruk `.cmp`-metoden for å få ut en type som sier om inputet er mindre, større eller likt det hemmelige tallet.

💡 Tips: `match` kan være nyttig

<details>
<summary>🚨 Løsningsforslag</summary>

`parsed_input.cmp(&secret_number)` gjør susen. Vi må bruke `&secret_number` her fordi `cmp` ikke trenger eierskap over inputet og bare vil låne en referanse for å lese tallet. Akkurat når det gjelder tall er dette ikke nødvendig, fordi tall kan kopieres billig, men `cmp` er designet på en generisk måte, så for større typer er det ikke mulig å kopiere inputet bare for å lese det.

Bruk `match` på resultatet for å finne de tre tilstandende, som sier om inputet er mindre, større eller likt det hemmelige tallet. Bruk gjerne en code action i IDE-en din til å fylle ut de manglende patternsene, eller skriv følgende:

```rust
println!("You guessed {input}!");
match parsed_input.cmp(&secret_number) {
    std::cmp::Ordering::Less => println!("Your guess is too small"),
    std::cmp::Ordering::Greater => println!("Your guess is too big"),
    std::cmp::Ordering::Equal => {
        println!("Congratulations, you win!");
        return;
    }
}
```

</details>

---

### 5. Tilfeldig genererte hemmeligheter

Det er litt kjedelig at vi har hardkodet det hemmelige tallet, så la oss generere et tilfeldig et.

🏆 Legg til [`rand`](https://docs.rs/rand/latest/rand/) og bruk det til å generere et tall mellom 1 og 100 (inklusivt).

💡 Tips: Ikke glem av `cargo add`

💡 Tips: Dokumentasjonen til `rand` er god, kanskje de har en måte å *gen*erere en _range_ på?

<details>
<summary>🚨 Løsningsforslag</summary>

Bruk `cargo add` og navnet på dependencyen og kjør:

```shell
> cargo add rand
    Updating crates.io index
      Adding rand v0.8.5 to dependencies.
             Features:
             + alloc
             + getrandom
             + libc
             + rand_chacha
             + std
             + std_rng
             - log
             - min_const_gen
             - nightly
             - packed_simd
             - serde
             - serde1
             - simd_support
             - small_rng
```

All outputet er alle ekstra features `rand` støtter, så `+` er de som er aktivert og `-` er ikke aktiverte. Vi trenger ikke å tenke på disse for nå.

Endre koden for ditt hemmelige tall til:

```rust
let secret_number = rand::thread_rng().gen_range(1..=100);
```

Dette betyr i praksis:

- `rand` navnet på dependencyen
- `::thread_rng()` gå inn i modulen og hent funksjonen `thread_rng` og kall på den. Denne er en random number generator som seeder seg selv og gir kryptografisk sikre tilfeldig-genererte tall, som er unike per tråd. Litt overkill, men jaja.
- `.gen_range(1..=100)` generer et tilfeldig tall i en viss range, denne gangen mellom 1 og 100 (inklusivt).

</details>

---

### 6. Loops

Supert! Vi kan nå kjøre spillet og få nye tall når vi kjører det, men vi har bare ett gjett per nå. På tide å la brukeren gjette flere ganger før vi avslutter programmet.

🏆 Lag en loop som lar brukeren gjette flere ganger til de får rett

<details>
<summary>🚨 Løsningsforslag</summary>

Sleng en `loop {}` rundt koden din så er du nesten i mål:

```rust
loop {
    println!("Please input your guess.");
    let mut input = String::new();
    std::io::stdin()
        .read_line(&mut input)
        .expect("Failed to read line");

    println!("You guessed {input}");

    // Trim the input number to handle whitespace
    let guess: u32 = input.trim().parse().expect("Please input a number");

    match guess.cmp(&secret_number) {
        std::cmp::Ordering::Less => println!("Your guess is too small"),
        std::cmp::Ordering::Greater => println!("Your guess is too big"),
        std::cmp::Ordering::Equal => {
            println!("Congratulations, you win!");
            return;
        }
    }
}
```

</details>

---

### 7. Tell antall gjett

Supert! Nå har vi et fungerende spill. La oss lage en slags high-score for brukeren så de ser hvor mange gjett de har.

🏆 Tell hvor mange ganger brukeren har prøvd å gjette. Print ut noe a la "Du har gjettet x ganger" for hvert nye gjett, men ikke første gangen (ikke print "Du har gjettet 0 ganger").

💡 Tips: _hvis_ du ikke klarer denne bør du sjekke ut intro-presentasjonen igjen

<details>
<summary>🚨 Løsningsforslag</summary>

Lag en variabel før loopen:

```rust
let mut num_guesses = 0;
```

Inni loopen, sørg for at vi ikke printer første gangen:

```rust
if num_guesses > 0 {
    println!("You've guessed {num_guesses} times");
}
```

På slutten av loopen, sleng på:

```rust
num_guesses += 1;
```

</details>

---

### Ferdig! Final touches?

Supert! Nå er du ferdig med en kort liten intro til Rust. På tide å ta et nytt steg videre. Enten du ønsker å jobbe videre med dette eller om du ønsker å gjøre noe litt større.

Dersom du synes det var litt gøy å lage et slikt lite spill, hvorfor ikke se på muligheten for å utvide spillet med litt mer funksjonalitet?

- Hva med å lage et scoreboard som viser færreste gjett og hvilket hemmelig tall som var?
- Legg til litt farger på outputen med [`colored`](https://docs.rs/colored/latest/colored/)
- La brukerne konfigurere så det ikke alltid er et tall mellom 1 og 100. Hva med at det også tilfeldig genereres. Eller at du henter [input args](https://doc.rust-lang.org/stable/std/env/fn.args.html) og lar brukeren skrive makstallet istedenfor 100?
- Eller bruk [clap](https://docs.rs/clap/latest/clap/) for å la spilleren skrive både input minimum og maksimum og alt mulig annet
- Kanske la gjettingen være noe annet enn et heltall? Hva med et flyttall? Hva med en string? 🤔
- Hva med å lage en quiz som CLI-verktøy? Hvorfor ikke gjøre den helt interaktiv med [ratatui](https://docs.rs/ratatui/latest/ratatui/)?

---

## Del 2 - Test-drevet workshop

Etter du har gjort dette gjettespillet kan du gå over til å lære mer om Rust, syntaksen og grunnleggende typer.
Følgende workshop er satt sammen av mange mindre oppgaver der man skal fylle ut funksjoner og få tester til å passere, samtidig som man lærer mer om Cargo og verktøyene rundt Rust.

Workshopen ligger på denne lenken: https://github.com/IsakSundeSingh/rust-exercises

---

## Del 3 - Lag din egen `ls` i Rust

Om du kjenner at du kan Rust litt greit nok kan du gå over til en workshop om å lage et CLI-verktøy i Rust, en klone av det klassiske verktøyet `ls` som lister ut filer i terminalen. Det høres kanskje kort ut, men det er mye artig man kan gjøre om man vil.

Workshopen ligger på denne lenken: https://github.com/IsakSundeSingh/rust-file-explorer-workshop

---
