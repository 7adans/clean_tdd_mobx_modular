# 🧪 Flutter Clean Architecture, built test-first

**A Flutter app where every layer, from data sources and repositories to use cases and state, was written test-first. The result: 11 test suites covering the full feature, with tests that run without a device or network.**

Most "Clean Architecture" samples show the folder structure but skip the reason for it: being able to test the business logic in isolation. This project goes the other way. I wrote each unit's test first, then the code to make it pass. The architecture is what came out of that process.

The feature itself is simple on purpose (fetch trivia for a number, or a random one, with offline cache). That keeps the attention on the engineering.

## 🧱 Architecture

```
lib/app/
├── core/                      # cross-cutting: errors, network info, base UseCase, input converter
└── modules/number_trivia/
    ├── domain/                # pure Dart: entities, repository contracts, use cases
    ├── data/                  # models (JSON), remote + local data sources, repository impl
    └── presentation/          # MobX stores + widgets
```

- **Dependency rule:** `presentation → domain ← data`. The domain layer has no Flutter, HTTP or storage imports.
- **Errors as values:** repositories return `Either<Failure, T>` (dartz) instead of throwing, so every failure path is explicit and testable.
- **Offline-first:** the repository checks connectivity, serves from the remote source when online, caches the last result, and falls back to the cache when offline.
- **Modules:** routing and DI per feature with Flutter Modular.

## ✅ Tests

| Layer | What's covered |
|---|---|
| Core | `NetworkInfo`, `InputConverter` |
| Data | remote data source (HTTP mocked), local data source (cache), model JSON (de)serialization with fixtures, repository online/offline branches |
| Domain | `GetConcreteNumberTrivia`, `GetRandomNumberTrivia` |
| Presentation | `NumberTriviaStore` state transitions |

```bash
flutter test
```

## 🛠️ Stack

Flutter · Dart · MobX · Flutter Modular · dartz (`Either`) · Equatable · Mockito · http · SharedPreferences

## 💡 Note

This project dates from 2021 (Dart 2, pre-null-safety). Today I'd write the same architecture with BLoC or Riverpod, `get_it`/`injectable` and `mocktail`. The layering and the test-first discipline stay the same, and that's the part this repo is meant to show.

Based on Reso Coder's Clean Architecture & TDD course, re-implemented with MobX + Modular instead of BLoC.
