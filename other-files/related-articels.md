# Первичные источники по теме

## System Verilog

### Соединение C и Verilog (DPI / VPI)

- Ссылка: https://veripool.org/guide/latest/connecting.html
- PDF: https://veripool.org/ftp/verilator_doc.pdf
- Тип: документация.

- Старый стандарт - VPI
IEEE Std 1364-2005

Современный стандарт
IEEE Std 1800. SystemVerilog — Unified Hardware Design, Specification, and Verification Language. https://standards.ieee.org/ieee/1800/7743/ (1800-2023)

**Обзор.** Практическое описание DPI-C и VPI в Verilator: `import "DPI-C"`, заголовок `svdpi.h`, context-функции, ограничения. FAQ прямо советует DPI вместо ручного лазанья в сигналы.

**Зачем в работе.** Это руководство по переносимой реализации `sv_printf`: прототип на Verilator должен совпадать по DPI с тем, что потом пойдёт в VCS.

### Synopsys. Verification Continuum VCS User Guide

- Публичный устаревший срез: https://users.ece.utexas.edu/~patt/10s.382N/handouts/vcs.pdf
- Более свежий срез: https://iccircle.com/static/upload/img20240131091021.pdf
- Тип: руководство пользователя.

**Зачем в работе.** Основной документ по целевому симулятору.

### DPI-C ABI
2.3. Rich D. et al. DPIRedux. Functionality. Speed. Optimization. DVCon.
- ссылка: https://dvcon-proceedings.org/wp-content/uploads/dpi-redux-functionality-speed-optimization.pdf
- близкий whitepaper siemens: https://resources.sw.siemens.com/it-it/white-paper-making-your-dpi-c-interface-a-fast-river-of-data/
- тип: статья dvcon.

**Зачем в работе.** Статья про тонкости ABI при DPI-C подключении

### Особенности написания высокопроизводительного кода с DPI-C.
«C you on the faster side: Accelerating SV DPI based co-simulation». DVCon
- Ссылка: https://dvcon-proceedings.org/wp-content/uploads/c-you-on-the-faster-side-accelerating-sv-dpi-based-co-simulation.pdf
- Тип: статья DVCon.

**Зачем в работе.** Про overhead DPI - частота вызовов важнее, чем сама C-функция.

### Производительность и методика замеров времени на симуляторек
«Pedal faster or make your verification environment more efficient». DVCon.
- Ссылка: https://dvcon-proceedings.org/wp-content/uploads/pedal-faster-or-make-your-verification-environment-more-efficient-you-choose.pdf
- Тип: статья DVCon.

## Dictionary based logging

### Nanolog
Yang S., Park S. J., Ousterhout J. NanoLog: A Nanosecond Scale Logging System. USENIX ATC, 2018
- Страница: https://www.usenix.org/conference/atc18/presentation/yang-stephen
- PDF: https://www.usenix.org/system/files/conference/atc18/atc18-yang.pdf
- Тип: статья конференции.

. Yang S. NanoLog: A Nanosecond Scale Logging System. PhD thesis, Stanford, 2020.
- Ссылка: https://web.stanford.edu/~ouster/cgi-bin/papers/YangPhD.pdf
- Тип: диссертация.

**Зачем в работе.** Продвинутая библиотека для идеи со словарным выводом


### Zephyr Project. Dictionary-based logging.

- Обзор подсистемы: https://docs.zephyrproject.org/latest/services/logging/index.html
- Sample: https://docs.zephyrproject.org/latest/samples/subsys/logging/dictionary/README.html
- Тип: документация + код.

**Зачем в работе.** Похожая на описанную идею, но более динамическая


## Обработка форматирования в симуляторе

### Semihosting в симуляции

Базовое описание в документации RISC-V
- PDF: https://docs.riscv.org/reference/semihosting/_attachments/riscv-semihosting.pdf
- Репозиторий: https://github.com/riscv-non-isa/riscv-semihosting
- Тип: стандарт.

Более подробное описание для ARM - но оно схоже с riscv
- Актуальный ABI: https://github.com/ARM-software/abi-aa (файл `semihosting.pdf` в релизах)
- Исторический ADS Debug Target Guide: https://support.arm.com/documentation/dui0058/d/semihosting/semihosting-swis
- Тип: стандарт.

**Зачем в работе.** Semihosting — прошивка ходит в консоль/файлы хоста через отладчик или симулятор, без своего стека I/O. Это стандартизованный конкурент ваших MMIO-регистров. В обзоре нужно сравнить: semihosting требует trap и часто синхронный round-trip - медлеенее MMIO доступа, но нужно указать;

### ABI между тестируемым кодом и форматером

RISC-V International. RISC-V ABIs Specification (calling convention + ELF).
- PDF: https://docs.riscv.org/reference/abi/_attachments/riscv-abi.pdf
- ELF-глава: https://docs.riscv.org/reference/abi/v1.0/riscv-elf-object-files.html
- Тип: стандарт.

**Зачем в работе.** как передаются аргументы, как устроен ELF для RV32. Нужно и для DPI-совместимой C-библиотеки, и для вытаскивания `.dbl_fmt` из прошивки. DBL кладёт формат-строки в dedicated ELF-section; без ABI/ELF это нельзя описать аккуратно. Для `sv_printf` ABI нужен, чтобы хостовый `printf` правильно разобрал `uint32_t args[8]`.

### Схожая идея, но с semihosting

- Описание: SEGGER Semihosting (https://www.segger.com/products/development-tools/embedded-studio/technology/semihosting)
- Протокол: SEGGER Semihosting, операция Writef (https://kb.segger.com/SEGGER_Semihosting)
- В Ozone это SysWritef (0x40): в блоке аргументов два указателя — на формат-строку и на va_list. Спецификация: Semihosting with Ozone (https://kb.segger.com/Semihosting_with_Ozone)


## Близкое к теме

### Общий гайд по RISC-V

Patterson D., Waterman A. The RISC-V Reader: An Open Architecture Atlas. Strawberry Canyon, 2017.

- Тип: книга.
- Сайт: http://riscvbook.com/

### Общий гайд по System Verilog
Spear C., Tumbush G. SystemVerilog for Verification. 3rd ed. Springer, 2012.

**Зачем в работе.** Практический SV для верификации: testbench, DPI в контексте UVM-мира.

### Про линкер
Levine J. Linkers and Loaders. Morgan Kaufmann, 2000.
- Тип: книга.
- Черновики: https://www.iecc.com/linker/

**Зачем в работе.** Может быть полезно при описании сборки словаря DBL (`.dbl_fmt`, `objcopy -R`).

