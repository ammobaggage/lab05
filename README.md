# Домашнее задание 5 (rev 3)
---
[![Coverage Status](https://coveralls.io/repos/github/ammobaggage/lab05/badge.svg?branch=main)](https://coveralls.io/github/ammobaggage/lab05?branch=main)

---
## Задание 1
>Скачал файлы из оригинального репрезитория 
>Создал CMakeLists.txt
```
cmake_minimum_required(VERSION 3.2)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

option(BUILD_TESTS "Build tests" ON)

enable_testing()

project(banking)

set(Sources
    banking/Account.cpp
    banking/Transaction.cpp
)
set(Headers
    banking/Account.h
    banking/Transaction.h
)

add_library(banking STATIC ${Sources} ${Headers})
target_include_directories(banking PUBLIC banking)

if(BUILD_TESTS)
    enable_testing()
    add_subdirectory(third-party/gtest)
    add_executable(my_test tests/tests.cpp)
    target_link_libraries(my_test
        gtest_main
        gmock_main
        banking
    )
    add_test(NAME my_test COMMAND my_test)
endif()

```
---
## Задание 2
>Прописал Transaction.cpp
```
#include "Transaction.h"

#include <cassert>
#include <iostream>
#include <stdexcept>

#include "Account.h"

namespace {
// RAII
struct Guard {
  Guard(Account& account) : account_(&account) { account_->Lock(); }

  ~Guard() { account_->Unlock(); }

 private:
  Account* account_;
};
}  // namespace

Transaction::Transaction() : fee_(1) {}

Transaction::~Transaction() {}

bool Transaction::Make(Account& from, Account& to, int sum) {
  if (from.id() == to.id()) throw std::logic_error("invalid action");

  if (sum < 0) throw std::invalid_argument("sum can't be negative");

  if (sum < 100) throw std::logic_error("too small");

  if (fee_ * 2 > sum) return false;

  Guard guard_from(from);
  Guard guard_to(to);

  Credit(to, sum);

  bool success = Debit(from, sum + fee_);
  if (!success) to.ChangeBalance(-sum);

  SaveToDataBase(from, to, sum);
  return success;
}

void Transaction::Credit(Account& accout, int sum) {
  assert(sum > 0);
  accout.ChangeBalance(sum);
}

bool Transaction::Debit(Account& accout, int sum) {
  assert(sum > 0);
  if (accout.GetBalance() > sum) {
    accout.ChangeBalance(-sum);
    return true;
  }
  return false;
}

void Transaction::SaveToDataBase(Account& from, Account& to, int sum) {
  std::cout << from.id() << " send to " << to.id() << " $" << sum << std::endl;
  std::cout << "Balance " << from.id() << " is " << from.GetBalance()
            << std::endl;
  std::cout << "Balance " << to.id() << " is " << to.GetBalance() << std::endl;
}
```
---
## Задание 3
(((
---
## Задание 4
>Настроил coveralls.io, плашка-вывод находится в самом начале отчета

**Карев Георгий | ИУ8-21** 
