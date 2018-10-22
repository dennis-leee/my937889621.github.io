---
layout:     post
title:      "系统分析与设计 Homework - 5"
subtitle:   "领域建模"
date:       2018-04-29 12:00:00 +0800
author:     Dennis
header-img: ""
tags:
    - 学习
    - 系统分析与设计
    - Homework
---

# 1. 领域建模
## A. 阅读Asg_RH文档，按用例构建领域模型。
- 按Task2要求，使用工具UMLet绘制。
- 说明：请不要受PCMEF层次结构影响。识别实体（E）和中介实体（M）
  - 在单页面应用中，E一般与数据库构建有关，M一般与store模式有关
  - 在java web应用中，E一般与数据库构建有关，M一般与session有关
![Class Model](/img/in-post/SAAD-homework/hw5/classModel.png)

## B. 数据库建模（E-R模型）
- 按Task3要求，给出系统的E-R模型（数据逻辑模型）
![ER Model](/img/in-post/SAAD-homework/hw5/ERModel.png)

- 建模工具PowerDesigner或开源工具OpenSystemArchitect
- 导出MySQL物理数据库的脚本

```
-- MySQL Workbench Forward Engineering

SET @OLD_UNIQUE_CHECKS=@@UNIQUE_CHECKS, UNIQUE_CHECKS=0;
SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS=0;
SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='TRADITIONAL,ALLOW_INVALID_DATES';

-- -----------------------------------------------------
-- Schema mydb
-- -----------------------------------------------------

-- -----------------------------------------------------
-- Schema mydb
-- -----------------------------------------------------
CREATE SCHEMA IF NOT EXISTS `mydb` DEFAULT CHARACTER SET utf8 ;
USE `mydb` ;

-- -----------------------------------------------------
-- Table `mydb`.`Traveler`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `mydb`.`Traveler` (
  `id` INT UNSIGNED NOT NULL,
  `full name` VARCHAR(20) NOT NULL,
  `email address` VARCHAR(45) NULL,
  `is smoking` TINYINT(1) NULL,
  PRIMARY KEY (`id`),
  UNIQUE INDEX `id_UNIQUE` (`id` ASC))
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `mydb`.`CreditCard`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `mydb`.`CreditCard` (
  `card number` INT UNSIGNED NOT NULL,
  `user id` INT UNSIGNED NOT NULL,
  `security code` VARCHAR(45) NOT NULL,
  PRIMARY KEY (`card number`, `user id`),
  UNIQUE INDEX `card number_UNIQUE` (`card number` ASC),
  UNIQUE INDEX `user id_UNIQUE` (`user id` ASC),
  CONSTRAINT `user id`
    FOREIGN KEY (`user id`)
    REFERENCES `mydb`.`Traveler` (`id`)
    ON DELETE CASCADE
    ON UPDATE CASCADE)
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `mydb`.`Location`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `mydb`.`Location` (
  `code` VARCHAR(20) NOT NULL,
  `name` VARCHAR(45) NOT NULL,
  `is hot` TINYINT(1) NOT NULL,
  PRIMARY KEY (`code`),
  UNIQUE INDEX `code_UNIQUE` (`code` ASC))
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `mydb`.`Hotel`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `mydb`.`Hotel` (
  `id` INT UNSIGNED NOT NULL,
  `location code` VARCHAR(20) NOT NULL,
  `name` VARCHAR(45) NOT NULL,
  `star` INT UNSIGNED NOT NULL,
  `description` VARCHAR(450) NULL,
  PRIMARY KEY (`id`, `location code`),
  UNIQUE INDEX `id_UNIQUE` (`id` ASC),
  UNIQUE INDEX `location code_UNIQUE` (`location code` ASC),
  CONSTRAINT `loaction`
    FOREIGN KEY (`location code`)
    REFERENCES `mydb`.`Location` (`code`)
    ON DELETE CASCADE
    ON UPDATE CASCADE)
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `mydb`.`Room`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `mydb`.`Room` (
  `id` INT UNSIGNED NOT NULL AUTO_INCREMENT,
  `affiliated hotel id` INT UNSIGNED NOT NULL,
  `list price` VARCHAR(45) NULL,
  `is Avaliable` TINYINT(1) NOT NULL,
  `type` VARCHAR(45) NOT NULL,
  PRIMARY KEY (`id`, `affiliated hotel id`),
  UNIQUE INDEX `id_UNIQUE` (`id` ASC),
  UNIQUE INDEX `affiliated hotel_UNIQUE` (`affiliated hotel id` ASC),
  CONSTRAINT `affiliated hotel id`
    FOREIGN KEY (`affiliated hotel id`)
    REFERENCES `mydb`.`Hotel` (`id`)
    ON DELETE CASCADE
    ON UPDATE CASCADE)
ENGINE = InnoDB;


-- -----------------------------------------------------
-- Table `mydb`.`Reservation`
-- -----------------------------------------------------
CREATE TABLE IF NOT EXISTS `mydb`.`Reservation` (
  `traveler id` INT UNSIGNED NOT NULL,
  `hotel id` INT UNSIGNED NOT NULL,
  `room id` INT UNSIGNED NOT NULL,
  PRIMARY KEY (`traveler id`, `hotel id`, `room id`),
  UNIQUE INDEX `traveler id_UNIQUE` (`traveler id` ASC),
  UNIQUE INDEX `hotel id_UNIQUE` (`hotel id` ASC),
  UNIQUE INDEX `room id_UNIQUE` (`room id` ASC),
  CONSTRAINT `traveler`
    FOREIGN KEY (`traveler id`)
    REFERENCES `mydb`.`Traveler` (`id`)
    ON DELETE CASCADE
    ON UPDATE CASCADE,
  CONSTRAINT `hotel`
    FOREIGN KEY (`hotel id`)
    REFERENCES `mydb`.`Hotel` (`id`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION,
  CONSTRAINT `room`
    FOREIGN KEY (`room id`)
    REFERENCES `mydb`.`Room` (`id`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION)
ENGINE = InnoDB;


SET SQL_MODE=@OLD_SQL_MODE;
SET FOREIGN_KEY_CHECKS=@OLD_FOREIGN_KEY_CHECKS;
SET UNIQUE_CHECKS=@OLD_UNIQUE_CHECKS;
```

- 简述 数据库逻辑模型 与 领域模型 的异同
  - 相同点：都有实体的概念，以及实体间关系的表达
  - 不同点：
    1. 领域模型又称业务实体模型，是根据实际用例来建立的。其不需要考虑太多的实现细节，更注重的是表达实体以及其之间的关系，为了更好地表示以及理解这些关系，还引进了中介实体的概念。
    2. 数据库逻辑模型就是领域模型的具体化。该模型描述的是用户需求在技术实现上的逻辑关系，其没有中介实体的概念，取而代之的是主键、外键等关系约束。
