# tauri
Rust tauri library bindings for H#.

Bindingi Tauri 2.x dla HackerScript. Cały shim jest napisany w HackerScript
(`lib/mod.hcs`) - funkcje `fun` z blokami `native {Rust} [ ... ]`, bez
osobnego crate'a Rust.

## Model

HackerScript nie ma domknięć, więc most JS <-> HackerScript to jedna funkcja
aplikacji (w `cmd/main.hcs`):

```
fun tauri_handle(app: Int, command: Str, payload: Str) -> Str
```

Każde `invoke("nazwa", {...})` z JS i każde zdarzenie z `tauri_listen` trafia
do niej. `app` to uchwyt aplikacji dla `tauri_emit`, `tauri_window_*`,
`tauri_path`. Payload i wynik to JSON; błąd: `tauri_err("opis")`.
Komendy cyklu życia: `tauri:setup`, `tauri:exit`.

## API (`lib/mod.hcs`)

| Grupa | Funkcje |
|---|---|
| Start | `tauri_run` |
| JSON/odpowiedzi | `tauri_ok`, `tauri_err`, `tauri_json_quote`, `tauri_arg`, `tauri_arg_int`, `tauri_arg_bool` |
| Zdarzenia | `tauri_emit`, `tauri_emit_to`, `tauri_listen`, `tauri_unlisten`, `tauri_is_event`, `tauri_event_name` |
| Okna | `tauri_window_action`, `tauri_window_create`, `tauri_window_title`, `tauri_window_is_visible`, `tauri_window_show/hide/close/minimize/maximize/center/set_title/eval` |
| Aplikacja | `tauri_app_name`, `tauri_app_version`, `tauri_path`, `tauri_exit` |

## Użycie

```
Virus.hk
cmd/main.hcs          # tauri_handle + main() { tauri_run() }
tauri/tauri.conf.json # + capabilities/, icons/icon.png, frontend (frontendDist)
```

Gdy istnieje `tauri/tauri.conf.json`, `virus build` kopiuje katalog `tauri/`
do crate'a i dopisuje `build.rs` z `tauri_build::build()` (rozszerzenie
toolchainu HackerScript, patrz `docs/TAURI.md` w repozytorium HackerScript).
Pełny przykład: `examples/hello` (jego `cmd/tauri.hcs` to kopia
`lib/mod.hcs`).

## Wymagania

Rust 1.90+ (tyle wymaga `tauri` 2.12) i zależności systemowe Tauri; na
Linuksie m.in. WebKitGTK 4.1, GTK3, libsoup3.

## Status

Kod nie był kompilowany ani uruchamiany: w środowisku, w którym powstał,
nie było `hackerc` ani Rusta. Nazwy metod Tauri sprawdzono ze źródłami
`tauri 2.12.1`, składnię HackerScript z kodem i dokumentacją kompilatora.
Pierwszy `virus build` może wymagać drobnych poprawek.
