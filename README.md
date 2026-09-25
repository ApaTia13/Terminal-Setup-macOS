# 🖥️ Terminal Setup для macOS
> Настройка терминала: zsh, автодополнение, Nerd Font и `eza`.

---

## 📋 Содержание

- [1. Homebrew](#1-homebrew)
- [2. Игнор регистра при Tab](#2-игнор-регистра-при-tab)
- [3. Плагины zsh](#3-плагины-zsh)
- [4. Nerd Font](#4-nerd-font)
- [5. eza — замена ls](#5-eza--замена-ls)
- [6. Итоговый ~/.zshrc](#6-итоговый-zshrc)
- [7. Проверка](#7-проверка)
- [8. Откат](#8-откат)

---

## 1. Homebrew

Если ещё не установлен:

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

> ⚠️ **Никогда не запускай `brew` от `root`.** Если в приглашении `#` — сначала `exit`.

Проверка:

```bash
brew doctor
# → Your system is ready to brew.
```

---

## 2. Игнор регистра при Tab

По умолчанию zsh чувствителен к регистру: `cd /u` + `Tab` не дополнится до `/Users`.

Добавь в `~/.zshrc`:

```bash
zstyle ':completion:*' matcher-list 'm:{a-zA-Z}={A-Za-z}'
```

---

## 3. Плагины zsh

### Установка

```bash
brew install zsh-autosuggestions zsh-syntax-highlighting
brew install zsh-completions fzf

# fzf-tab ставится вручную (нет в brew)
mkdir -p ~/.zsh
git clone https://github.com/Aloxaf/fzf-tab ~/.zsh/fzf-tab
```

### Что делают

| Плагин | Зачем |
|---|---|
| `zsh-autosuggestions` | Серая подсказка из истории → `→` принимает |
| `zsh-syntax-highlighting` | Подсветка: 🟢 команда ок, 🔴 опечатка |
| `zsh-completions` | Больше определений для автодополнения |
| `fzf-tab` | Интерактивное меню на `Tab` с поиском |
| `fzf` | Нечёткий поиск (`Ctrl+R`, `Ctrl+T`, `Alt+C`) |

### ⚠️ Порядок загрузки — критичен

```
fpath (zsh-completions)  →  compinit  →  fzf-tab  →  autosuggestions  →  syntax-highlighting
```

`syntax-highlighting` **всегда последним**. Если поставить его раньше — подсветка отвалится.

---

## 4. Nerd Font

Без Nerd Font иконки в `eza` будут квадратиками `?`.

```bash
brew install --cask font-jetbrains-mono-nerd-font
```

Затем в настройках терминала выбери шрифт **`JetBrainsMono Nerd Font`**.

<details>
<summary>Где сменить шрифт</summary>

- **Terminal.app:** `Cmd + ,` → Профили → Текст → Изменить → `JetBrainsMono Nerd Font`
- **iTerm2:** `Cmd + ,` → Profiles → Text → Font → `JetBrainsMono Nerd Font` (+ галочка `Use a different font for non-ASCII`)

</details>

Альтернативы:

```bash
brew install --cask font-hack-nerd-font
brew install --cask font-fira-code-nerd-font
brew install --cask font-meslo-lg-nerd-font
```

---

## 5. eza — замена ls

```bash
brew install eza
```

Алиасы в `~/.zshrc`:

```bash
alias ls='eza --icons --group-directories-first'
alias ll='eza -lh --icons --git --group-directories-first'
alias la='eza -lah --icons --git --group-directories-first'
alias lt='eza --tree --icons --level=2'
```

| Команда | Что показывает |
|---|---|
| `ls` | Список с иконками, папки сверху |
| `ll` | Длинный формат + Git-статус |
| `la` | То же + скрытые файлы |
| `lt` | Дерево на 2 уровня |

Полезные разовые флаги:

```bash
eza -l --sort=size          # по размеру
eza -l --sort=modified      # по дате
eza -lh --total-size        # размер папок целиком
eza -la --tree              # дерево со скрытыми
```

---

## 6. Итоговый ~/.zshrc

Собранный конфиг — в правильном порядке:

```bash
# ─── zsh-completions (ДО compinit) ───────────────
fpath=($(brew --prefix)/share/zsh-completions $fpath)

# ─── Инициализация автодополнения ────────────────
autoload -Uz compinit && compinit

# ─── Игнор регистра при Tab ──────────────────────
zstyle ':completion:*' matcher-list 'm:{a-zA-Z}={A-Za-z}'

# ─── fzf-tab (ПОСЛЕ compinit) ────────────────────
source ~/.zsh/fzf-tab/fzf-tab.zsh

# ─── Автоподсказки ───────────────────────────────
source $(brew --prefix)/share/zsh-autosuggestions/zsh-autosuggestions.zsh

# ─── Подсветка синтаксиса (ВСЕГДА ПОСЛЕДНЯЯ) ─────
source $(brew --prefix)/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh

# ─── eza — замена ls ─────────────────────────────
alias ls='eza --icons --group-directories-first'
alias ll='eza -lh --icons --git --group-directories-first'
alias la='eza -lah --icons --git --group-directories-first'
alias lt='eza --tree --icons --level=2'
```

Применить:

```bash
source ~/.zshrc
```

---

## 7. Проверка

```bash
# Игнор регистра
cd /u          # + Tab → /Users
cd ~

# Плагины
brew <Tab>     # богатое автодополнение
# начни печатать команду из истории → серая подсказка → →

# fzf-tab
cd <Tab>       # интерактивное окно поиска

# eza
ls
ll
la
lt
```

Всё должно работать без ошибок, иконки — отображаться нормально, не квадратиками.

---

## 8. Откат

```bash
# Убрать алиасы и source-строки — просто удали их из ~/.zshrc
nano ~/.zshrc
source ~/.zshrc

# Удалить пакеты
brew uninstall eza zsh-autosuggestions zsh-syntax-highlighting zsh-completions fzf
rm -rf ~/.zsh/fzf-tab

# Шрифт
brew uninstall --cask font-jetbrains-mono-nerd-font
```

---

## 🔗 Ссылки

- [Homebrew](https://brew.sh)
- [Nerd Fonts](https://www.nerdfonts.com)
- [zsh-autosuggestions](https://github.com/zsh-users/zsh-autosuggestions)
- [zsh-syntax-highlighting](https://github.com/zsh-users/zsh-syntax-highlighting)
- [zsh-completions](https://github.com/zsh-users/zsh-completions)
- [fzf-tab](https://github.com/Aloxaf/fzf-tab)
- [eza](https://github.com/eza-community/eza)

---
