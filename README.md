
# Лабораторная работа 4.

Для решения понадобится установить Docker. Можно это делать как в виртуальной машине с установленным Linux, так и через WSL (главное на линукс подобной системе).

## Решение

1. Первым делом надо написать dockerfile. В него вставляем такой код.
```
# Базовый образ
FROM ubuntu:latest

RUN apt-get update && apt-get install -y \
    aalib-bin \
    && apt-get clean

WORKDIR /app

CMD ["aafire"]
```
*(clean удаляет кэш контейнера, а с WORKDIR проще работать внутри контейнера, и вроде как рекомендовано его использовать, чтобы не использовать абсолютные пути)* \
2. Собираем образ докерфайла в папке ```aafire-container```. Команда сборки: ```sudo docker build -t aafire-image ./aafire-container```. запуск контейнера - ```sudo docker run -it aafire-image```. \
3. Создаём сети Docker: ```sudo docker create lab4-network```. Запускаем контейнеры в одной сети: \
Для первого - ```sudo docker run -it --network lab4-network --name aafire-container aafire-image bash```. \
Для второго - ```sudo docker run -it --network lab4-network --name receiver-container debian:latest```. \
Первый контейнер - передаёт, второй - получает. Флаг ```--name``` и задал им названия контейнеров. ```bash``` в первом случае потребовался, потому что контейнер с ASCII-анимацией занимает консоль, и поэтому я не мог вводить команды. \
4. Устанавливаем ```netcat-openbsd``` на второй контейнера, чтобы можно было принимать сигналы (```-l -p``` - слушать 1234 порт):
```
apt-get update && apt-get install -y netcat-openbsd
nc -l -p 1234
```
5. Запустим передачу aafire - ```aafire | nc receiver-container 1234```. \
6. Наслаждаемся, как с передающего на принимающий контейнер передаётся ASCII-огонь:
![image](https://github.com/mxrget/linux-university-lab-4/blob/main/lab4-pic1.png)
7. Осознаём, что сделали не то, что просили в лабораторной. Устанавливаем ping: ```apt-get update && apt-get install -y iputils-ping```. \
8. Запускаем оба контейнера через bash, проводим пинг по имени. Он получается:
![image](https://github.com/mxrget/linux-university-lab-4/blob/main/lab4-pic1.png)
9. Готово. Контейнеры не только передают друг другу огонёк, но и пингуют друг друга. Можно их остановить.
