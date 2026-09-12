# pre-commit

`pre-commit` автоматично запускає перевірки перед `git commit`.

На macOS його можна встановити через Homebrew:

```bash
brew install pre-commit
```

Приклад конфігурації лежить у `tools/.pre-commit-config-example.yaml`. Скопіюйте його в корінь свого проєкту як `.pre-commit-config.yaml` і змініть під потреби проєкту.

Встановіть hooks у репозиторії:

```bash
pre-commit install
```

Щоб вручну запустити всі hooks для всіх файлів:

```bash
pre-commit run --all-files
```

Якщо для `clang-tidy` у конфігу вказано `-p=build`, директорія `build` має містити `compile_commands.json`. Якщо вона називається інакше, змініть значення `-p`.

# clang-format

`clang-format` відповідає за вигляд C/C++ коду, а не за логіку програми. Для лабораторних його можна спокійно використовувати, щоб підтримувати однаковий стиль.

Приклад конфігурації знаходиться у `tools/.clang-format-example`. Скопіюйте його в корінь проєкту як `.clang-format` і за потреби змініть.

Встановлення на macOS:

```bash
brew install llvm
```

Форматування одного файлу:

```bash
clang-format -i src/main.cpp
git diff
```

Якщо Homebrew LLVM не знаходиться через `PATH`, актуальний шлях до нього покаже команда:

```bash
brew --prefix llvm
```

У конфігу використовується `Standard: c++20`, але ця опція не замінює `-std=c++20` під час компіляції.

# clang-tidy

`clang-tidy` — статичний аналізатор для C/C++. Конфіг `tools/.clang-tidy-example` є лише прикладом: його не варто сліпо копіювати або запускати з автоматичними `fix-it` на всьому проєкті.

На ПОК частина diagnostics може бути недоречною, бо в курсі навмисно використовуються raw pointers, C arrays, pointer arithmetic, manual memory management, C API, ABI та інші low-level конструкції. Для АКС `clang-tidy` може бути особливо корисним, але набір checks все одно варто адаптувати під конкретний проєкт.

Щоб аналіз був коректним, `clang-tidy` має знати справжні compiler flags, include paths, defines тощо. Для CMake найпростіше створити `compile_commands.json` так:

```bash
cmake -S . -B build -DCMAKE_EXPORT_COMPILE_COMMANDS=ON
```

Після цього файл буде тут:

```text
build/compile_commands.json
```

Приклад запуску:

```bash
clang-tidy src/main.cpp -p build
```

Якщо build directory має іншу назву, відповідно змініть `-p`.

У `.clang-tidy-example` є такий рядок:

```yaml
HeaderFilterRegex: '(^|.*/)PROJECT_NAME/.*\.(h|hh|hpp|hxx)$'
```

`PROJECT_NAME` — placeholder, його треба замінити на назву директорії власного проєкту. Наприклад:

```yaml
HeaderFilterRegex: '(^|.*/)pok-lab-01/.*\.(h|hh|hpp|hxx)$'
```

`HeaderFilterRegex` визначає, з яких header-файлів `clang-tidy` показуватиме diagnostics. Це не список `.cpp` файлів для запуску аналізатора.

# C++ Style Guide

[Google C++ Style Guide](https://google.github.io/styleguide/cppguide.html) не є обов'язковою вимогою курсу, але може бути хорошим reference щодо naming, структури файлів, include, classes та загального стилю. Не треба механічно переносити кожне правило в лабораторні, якщо воно конфліктує з умовою завдання або специфікою низькорівневого коду.
