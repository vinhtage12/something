# Table of contents

1. [Cấu trúc 1 Makefile](#C%E1%BA%A5u%20tr%C3%BAc%201%20Makefile)
	1. [Target](#Target)
	2. [Prerequisites / dependencies](#Prerequisites%20/%20dependencies)
	3. [Recipe](#Recipe)
	4. [Variables](#Variables)
	5. [Một Makefile thực tế](#M%E1%BB%99t%20Makefile%20th%E1%BB%B1c%20t%E1%BA%BF)
	6. [`.PHONY` là gì?](#%60.PHONY%60%20l%C3%A0%20g%C3%AC?)
	7. [7. Cách tư duy quan trọng nhất](#7.%20C%C3%A1ch%20t%C6%B0%20duy%20quan%20tr%E1%BB%8Dng%20nh%E1%BA%A5t)
2. [Variable và Parameter trong Makefile](#Variable%20v%C3%A0%20Parameter%20trong%20Makefile)
	1. [1. Variable](#1.%20Variable)
		1. [Khai báo](#Khai%20b%C3%A1o)
		2. [Sử dụng variable](#S%E1%BB%AD%20d%E1%BB%A5ng%20variable)
	2. [2. Parameter từ command line](#2.%20Parameter%20t%E1%BB%AB%20command%20line)
		1. [Giá trị mặc định](#Gi%C3%A1%20tr%E1%BB%8B%20m%E1%BA%B7c%20%C4%91%E1%BB%8Bnh)
	3. [3. Truyền flag/parameter cho command](#3.%20Truy%E1%BB%81n%20flag/parameter%20cho%20command)
	4. [4. Make variable vs Shell variable](#4.%20Make%20variable%20vs%20Shell%20variable)
		1. [Make variable](#Make%20variable)
		2. [Shell variable](#Shell%20variable)
		3. [Luồng xử lý](#Lu%E1%BB%93ng%20x%E1%BB%AD%20l%C3%BD)
	5. [5. Parameter không giống CLI flag](#5.%20Parameter%20kh%C3%B4ng%20gi%E1%BB%91ng%20CLI%20flag)
	6. [6. `.PHONY` cho target dạng command](#6.%20%60.PHONY%60%20cho%20target%20d%E1%BA%A1ng%20command)
	7. [7. Ví dụ tổng hợp](#7.%20V%C3%AD%20d%E1%BB%A5%20t%E1%BB%95ng%20h%E1%BB%A3p)
		1. [Tóm tắt](#T%C3%B3m%20t%E1%BA%AFt)
3. [Function trong Makefile](#Function%20trong%20Makefile)
	1. [1. Function là gì?](#1.%20Function%20l%C3%A0%20g%C3%AC?)
	2. [2. Function có parameter/argument](#2.%20Function%20c%C3%B3%20parameter/argument)
	3. [3. Một số function built-in quan trọng](#3.%20M%E1%BB%99t%20s%E1%BB%91%20function%20built-in%20quan%20tr%E1%BB%8Dng)
		1. [`shell`](#%60shell%60)
		2. [`wildcard`](#%60wildcard%60)
		3. [`patsubst`](#%60patsubst%60)
	4. [4. Function lồng nhau](#4.%20Function%20l%E1%BB%93ng%20nhau)
	5. [5. User-defined function](#5.%20User-defined%20function)
		1. [`$(1)`, `$(2)`, ...](#%60$(1)%60,%20%60$(2)%60,%20...)
	6. [6. Function vs Recipe](#6.%20Function%20vs%20Recipe)
		1. [Make function](#Make%20function)
		2. [Shell command](#Shell%20command)
		3. [Shell function](#Shell%20function)
	7. [7. Một số function nên nhớ](#7.%20M%E1%BB%99t%20s%E1%BB%91%20function%20n%C3%AAn%20nh%E1%BB%9B)
	8. [8. Function có thể kết hợp với Variable và Parameter](#8.%20Function%20c%C3%B3%20th%E1%BB%83%20k%E1%BA%BFt%20h%E1%BB%A3p%20v%E1%BB%9Bi%20Variable%20v%C3%A0%20Parameter)
	9. [9. Tóm tắt](#9.%20T%C3%B3m%20t%E1%BA%AFt)

# Cấu trúc 1 Makefile
Một `Makefile` về cơ bản là tập hợp các **target (mục tiêu)** và **recipe (các lệnh để tạo target đó)**.

Cấu trúc cơ bản:

```makefile
target: prerequisites
	command
	command
```

Ví dụ:

```makefile
build:
	echo "Building..."
	go build -o app .
```

Khi chạy:

```bash
make build
```

Make sẽ tìm target `build` rồi chạy các command bên dưới nó.
## Target

```makefile
build:
	echo "Building..."
```

`build` là **target** — thứ bạn muốn Make thực hiện.

Bạn chạy:

```bash
make build
```

---

## Prerequisites / dependencies

```makefile
build: main.go utils.go
	go build -o app .
```

Ở đây:

```text
build
 ├── main.go
 └── utils.go
```

`main.go` và `utils.go` là **dependencies** của `build`.

Make dùng thông tin này để quyết định khi nào cần chạy lại target.

---

## Recipe

Các dòng bên dưới target, **phải bắt đầu bằng tab**:

```makefile
build:
	echo "Building..."
	go build -o app .
```

Hai dòng `echo` và `go build` là **recipe**.

> Lưu ý: theo Makefile truyền thống, phải dùng **TAB**, không phải spaces.

---

## Variables

Bạn có thể định nghĩa biến:

```makefile
APP_NAME := myapp

build:
	go build -o $(APP_NAME) .
```

Sau đó:

```bash
make build
```

Make sẽ thay:

```text
$(APP_NAME)
```

thành:

```text
myapp
```

---

## Một Makefile thực tế

Ví dụ một project Go:

```makefile
APP_NAME := myapp

.PHONY: build run test clean

build:
	go build -o $(APP_NAME) .

run: build
	./$(APP_NAME)

test:
	go test ./...

clean:
	rm -f $(APP_NAME)
```

Có thể hiểu:

```text
make build
    ↓
go build -o myapp .

make run
    ↓
build
    ↓
./myapp

make test
    ↓
go test ./...

make clean
    ↓
rm -f myapp
```

## `.PHONY` là gì?

Đây là một thứ rất hay gặp:

```makefile
.PHONY: build test clean
```

Nó nói với Make rằng `build`, `test`, `clean` là **command/action**, không phải tên file.

Ví dụ bạn có:

```text
project/
├── Makefile
├── build
└── main.go
```

Nếu không có `.PHONY`, Make có thể thấy file `build` tồn tại và nghĩ:

> "À, target `build` đã tồn tại rồi, chắc không cần chạy."

Trong khi bạn muốn:

```bash
make build
```

luôn thực hiện action build.

---

## 7. Cách tư duy quan trọng nhất

Đừng nghĩ Makefile đơn giản là "file chứa một đống shell command".

Hãy nghĩ nó là **dependency graph**:

```text
             run
              │
            build
           /     \
      main.go   utils.go


             test
              │
        go test ./...
```

Make chủ yếu có nhiệm vụ:

> **"Target X phụ thuộc vào những thứ nào, và tôi có cần thực hiện lại X không?"**

Đây cũng là lý do Make ban đầu rất mạnh trong việc **build source code**, đặc biệt khi project có rất nhiều file và dependency.

# Variable và Parameter trong Makefile

## 1. Variable

Variable là giá trị được định nghĩa trong Makefile và có thể sử dụng lại ở nhiều nơi.

### Khai báo

```makefile
APP_NAME := myapp
ENV := development
VERSION ?= 1.0.0
```

Các toán tử thường gặp:

| Cú pháp | Ý nghĩa                                     |
| ------- | ------------------------------------------- |
| `:=`    | Gán giá trị ngay lập tức                    |
| `=`     | Gán đệ quy, giá trị có thể được resolve sau |
| `?=`    | Chỉ gán nếu variable chưa tồn tại           |
| `+=`    | Nối thêm giá trị                            |

### Sử dụng variable

Dùng `$(VARIABLE)`:

```makefile
APP_NAME := myapp

build:
	go build -o $(APP_NAME) .
```

Chạy:

```bash
make build
```

Make sẽ thực thi tương đương:

```bash
go build -o myapp .
```

---

## 2. Parameter từ command line

Make không có parameter theo kiểu function argument, mà thường truyền **variable từ command line**:

```bash
make build ENV=production VERSION=1.2.0
```

Makefile:

```makefile
build:
	echo "ENV=$(ENV)"
	echo "VERSION=$(VERSION)"
```

Kết quả:

```text
ENV=production
VERSION=1.2.0
```

Có thể hiểu:

```text
make build ENV=production VERSION=1.2.0
          │              │
          └── variable ──┘
```

### Giá trị mặc định

Dùng `?=`:

```makefile
ENV ?= development

build:
	echo "Building for $(ENV)"
```

```bash
make build
# Building for development

make build ENV=production
# Building for production
```

---

## 3. Truyền flag/parameter cho command

Có thể nhận một variable rồi truyền nó xuống command:

```makefile
build:
	go build $(GOFLAGS) -o app .
```

```bash
make build GOFLAGS="-v"
```

Make sẽ chạy:

```bash
go build -v -o app .
```

Ví dụ Docker:

```makefile
build:
	docker build $(DOCKER_ARGS) -t myapp .
```

```bash
make build DOCKER_ARGS="--no-cache --progress=plain"
```

---

## 4. Make variable vs Shell variable

Đây là điểm rất quan trọng.

### Make variable

```makefile
NAME := world

hello:
	echo "Hello $(NAME)"
```

`$(NAME)` được **Make xử lý trước khi command được đưa cho shell**.

### Shell variable

```makefile
hello:
	NAME=world; echo "Hello $$NAME"
```

`$$` được Make chuyển thành `$`, nên shell nhận:

```bash
NAME=world; echo "Hello $NAME"
```

### Luồng xử lý

```text
Makefile
   │
   │ $(NAME)
   ▼
Make
   │
   │ command đã được expand
   ▼
Shell
   │
   ▼
Program
```

---

## 5. Parameter không giống CLI flag

Thông thường dùng:

```bash
make deploy ENV=production VERSION=1.2.0
```

thay vì:

```bash
make deploy --env production --version 1.2.0
```

Cách đầu tiên là **idiomatic Make**.

Nếu thực sự cần cú pháp `--env`, phải tự xử lý argument trong Makefile hoặc shell script.

---

## 6. `.PHONY` cho target dạng command

Các target như `build`, `test`, `clean`, `deploy` thường nên khai báo:

```makefile
.PHONY: build test clean deploy
```

Ví dụ:

```makefile
.PHONY: build test

build:
	go build -o app .

test:
	go test ./...
```

`.PHONY` nói với Make rằng đây là **action/command**, không phải tên file.

---

## 7. Ví dụ tổng hợp

```makefile
APP_NAME := myapp
ENV ?= development
VERSION ?= 1.0.0
GOFLAGS ?=

.PHONY: build test clean

build:
	@echo "Building $(APP_NAME) v$(VERSION) for $(ENV)"
	go build $(GOFLAGS) -o $(APP_NAME) .

test:
	go test ./...

clean:
	rm -f $(APP_NAME)
```

Sử dụng:

```bash
make build
make build ENV=production
make build ENV=production VERSION=2.0.0
make build GOFLAGS="-v"
make test
make clean
```

### Tóm tắt

```text
Variable:
    APP_NAME := myapp
    $(APP_NAME)

Default variable:
    ENV ?= development

Command-line parameter:
    make build ENV=production

Pass flags:
    make build GOFLAGS="-v"

Make variable:
    $(NAME)

Shell variable:
    $$NAME

Action target:
    .PHONY: build
```

# Function trong Makefile

> **Lưu ý:** Function trong Makefile **khác function của Bash/Python**. Make có một hệ thống function riêng để xử lý text, file, variable, shell command,...

## 1. Function là gì?

Function có dạng:

```makefile
$(function arguments)
```

Ví dụ:

```makefile
NAME := hello

message:
	@echo $(shell echo "Hello $(NAME)")
```

Ở đây `shell` là **Make function**:

```makefile
$(shell ...)
```

Nó yêu cầu Make chạy một shell command và lấy output trả về.

---

## 2. Function có parameter/argument

Ví dụ function `subst`:

```makefile
TEXT := hello-world

result:
	@echo $(subst -, ,$(TEXT))
```

Cú pháp:

```text
$(subst FROM,TO,TEXT)
```

Ở đây:

```text
FROM = -
TO   = " "
TEXT = hello-world
```

Kết quả:

```text
hello world
```

Có thể hình dung:

```text
$(function arg1,arg2,arg3)
          │    │    │
          └────┴────┴── arguments
```

---

## 3. Một số function built-in quan trọng

### `shell`

Chạy shell command:

```makefile
DATE := $(shell date)

info:
	@echo "Current date: $(DATE)"
```

### `wildcard`

Tìm file:

```makefile
SOURCES := $(wildcard *.go)

info:
	@echo $(SOURCES)
```

Ví dụ thư mục có:

```text
main.go
user.go
config.go
```

thì:

```text
$(SOURCES)
```

sẽ trở thành:

```text
main.go user.go config.go
```

### `patsubst`

Thay pattern:

```makefile
SOURCES := main.c user.c config.c
OBJECTS := $(patsubst %.c,%.o,$(SOURCES))
```

Kết quả:

```text
main.o user.o config.o
```

---

## 4. Function lồng nhau

Function có thể dùng kết quả của function khác làm argument:

```makefile
SOURCES := $(wildcard *.c)
OBJECTS := $(patsubst %.c,%.o,$(SOURCES))
```

Luồng xử lý:

```text
*.c
 │
 ▼
wildcard
 │
 ▼
main.c user.c config.c
 │
 ▼
patsubst
 │
 ▼
main.o user.o config.o
```

Đây là pattern xuất hiện rất nhiều trong Makefile.

---

## 5. User-defined function

Make cũng cho phép tự định nghĩa function bằng `define`:

```makefile
define say_hello
	@echo "Hello $(1)"
endef
```

Gọi:

```makefile
hello:
	$(call say_hello,World)
```

Kết quả:

```text
Hello World
```

### `$(1)`, `$(2)`, ...

Khi dùng `call`, arguments được truyền vào:

```makefile
define greet
	@echo "Hello $(1), welcome to $(2)"
endef

hello:
	$(call greet,John,Makefile)
```

Kết quả:

```text
Hello John, welcome to Makefile
```

Có thể hiểu:

```text
$(call greet,John,Makefile)
             │     │
             │     └── $(2)
             └──────── $(1)
```

---

## 6. Function vs Recipe

Đây là điểm dễ nhầm.

### Make function

```makefile
FILES := $(wildcard *.go)
```

`wildcard` được **Make xử lý**.

### Shell command

```makefile
list:
	ls *.go
```

`ls` được **Shell thực thi**.

### Shell function

Bạn cũng có thể gọi shell command thông qua Make function:

```makefile
DATE := $(shell date)
```

Luồng:

```text
Make
 │
 ├── $(wildcard ...)
 │      └── Make xử lý
 │
 └── $(shell date)
        └── Make gọi Shell
               └── date
```

---

## 7. Một số function nên nhớ

| Function   | Mục đích                  | Ví dụ                          |
| ---------- | ------------------------- | ------------------------------ |
| `subst`    | Thay text                 | `$(subst a,b,text)`            |
| `patsubst` | Thay theo pattern         | `$(patsubst %.c,%.o,$(FILES))` |
| `wildcard` | Tìm file                  | `$(wildcard *.go)`             |
| `shell`    | Chạy shell command        | `$(shell pwd)`                 |
| `foreach`  | Lặp qua danh sách         | `$(foreach x,$(LIST),...)`     |
| `if`       | Điều kiện                 | `$(if $(DEBUG),yes,no)`        |
| `filter`   | Lọc danh sách             | `$(filter %.go,$(FILES))`      |
| `call`     | Gọi user-defined function | `$(call greet,John)`           |

---

## 8. Function có thể kết hợp với Variable và Parameter

Ví dụ khá thực tế:

```makefile
ENV ?= development
FILES := $(wildcard *.go)

.PHONY: info

info:
	@echo "Environment: $(ENV)"
	@echo "Go files: $(FILES)"
```

Chạy:

```bash
make info ENV=production
```

Make xử lý:

```text
ENV
 │
 └── command-line variable

FILES
 │
 └── $(wildcard *.go)
          │
          └── Make function
```

---

## 9. Tóm tắt

```text
Variable
    NAME := value
    $(NAME)

Function
    $(function arguments)

Built-in function
    $(wildcard *.go)
    $(shell pwd)
    $(subst a,b,text)
    $(patsubst %.c,%.o,$(FILES))

User-defined function
    define greet
        @echo "Hello $(1)"
    endef

Gọi function
    $(call greet,John)

Command-line parameter
    make build ENV=production
                │
                └── variable được truyền vào Make
```

**Cách tư duy ngắn gọn:**

```text
Variable  → lưu dữ liệu
Function  → xử lý dữ liệu
Parameter → truyền dữ liệu vào function/Make
Recipe    → command được Shell thực thi
```

Đặc biệt, hãy nhớ **`$(...)` trong Makefile không mặc định là shell syntax**. Nó thường là cú pháp để Make expand **variable hoặc function**.
