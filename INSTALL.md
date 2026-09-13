# GapFiller 安装与使用

## 归档说明

- `GapFiller.pl`：源码脚本（v1.11 修订版，改用 `Getopt::Std`、无需 perl4 的 `getopts.pl`，shebang 为 `/usr/bin/env perl`）
- `docs/`：用户手册、教程、许可协议，以及官方分发自带的 v1.11 README（`README_v1.11_distribution.txt`）
- `example/`：示例 scaffold 与 `library.txt`（与官方分发内一致）
- 官方 Linux x86_64 二进制分发（91 个文件，含捆绑的 Bowtie 与 BWA 及其编译产物）**不入代码树**；原始压缩包 `GapFiller_v1-11_linux-x86_64.tar.gz`（3,537,985 B，MD5 `1e68685987e21893e1321e9e3b89dac9`）见本仓库 Release `v1.11-linux-x86_64`
- 脚本版本取舍：分发包内的 `GapFiller.pl` 为 2012 年出厂版（`#!/usr/bin/perl` + perl4 的 `getopts.pl`，现代 Perl 下需另装该模块），本仓库保留修复后的源码版；出厂版仍完整保存在上述压缩包中
- 许可：GapFiller 允许学术/非营利机构自由使用，商业用途需另行取得授权（见 `docs/GapFiller-License-agreement_v1.11.pdf`）

## 安装

```bash
# 三代
tar zxf path/to/GapFiller_v1-11_linux-x86_64.tar.gz -C /path/to/install/

# 修复脚本格式
perl -p -i -e 's/\s*$/\n/' /path/to/install/GapFiller_v1-11_linux-x86_64/GapFiller.pl
chmod 644 /path/to/install/GapFiller_v1-11_linux-x86_64/*.pdf /path/to/install/GapFiller_v1-11_linux-x86_64/README

# 添加环境变量
echo 'PATH=$PATH:/path/to/install/GapFiller_v1-11_linux-x86_64/' >> ~/.bashrc
source ~/.bashrc
```

## 使用示例

```bash
mkdir -p /path/to/work/04.genome_assembling/GapFiller
cd /path/to/work/04.genome_assembling/GapFiller

# 建立数据符号链接
ln -s ../ALLPATHS-LG/allpathslg.fasta genome.fa
ln -s path/to/fragment.1.corrected.fastq fragment.1.fastq
ln -s path/to/fragment.2.corrected.fastq fragment.2.fastq
ln -s path/to/jumping.1.corrected.fastq jumping.1.fastq
ln -s path/to/jumping.2.corrected.fastq jumping.2.fastq

# 创建配置文件
# Lib1: 文库名
# bwa: 比对工具
# fragment.1.fastq fragment.2.fastq: 双端 reads
# 177: 平均插入长度
# 0.43: 标准差
# FR: 配对方向
echo "Lib1 bwa fragment.1.fastq fragment.2.fastq 177 0.43 FR
Lib2 bwa jumping.1.fastq jumping.2.fastq 3014 0.67 RF" > library.txt

# 运行 GapFiller
# -l library.txt: 配置文件
# -s genome.fa: 输入基因组序列
# -T 4: 使用 4 个线程
GapFiller.pl -l library.txt -s genome.fa -T 4
```

## 说明

- `path/to/`、`/path/to/install`、`/path/to/work` 均为占位路径，请按实际环境替换
- `library.txt` 每行格式：`文库名 比对工具 reads1 reads2 平均插入长度 标准差 配对方向`；`FR` 表示双端同向向内，`RF` 用于 mate-pair 文库
- 官方分发中捆绑的 `bowtie/`、`bwa/` 为 Linux x86_64 二进制，非该平台时请自行安装并在 `library.txt` 中指定对应比对工具
