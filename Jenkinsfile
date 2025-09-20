pipeline {
    agent any
    
    stages {
        stage('Clone Repository') {
            steps {
                git url: 'https://github.com/romo0208/mydataapp.git', branch: 'master'
            }
        }
        
//        stage('Run Script on WSL') {
//            steps {
//                sh '''
//                    # Скачиваем скрипт из текущего каталога проекта
//                    cp ./script.sh /mnt/c/path/to/your/wsl/folder/
//                    
//                    # Запускаем скрипт внутри WSL
//                    wsl bash /mnt/c/path/to/your/wsl/folder/script.sh
//                '''
//            }
//       }

        stage('Copy Files to Directory') {
            steps {
                sh '''
                    # Создаем целевую директорию, если её нет
                    mkdir -p /mnt/c/dev/a_data
                    # Копируем содержимое workspace рекурсивно
                    cp -R ./* /mnt/c/dev/a_data/
                    # Или используем rsync для более гибкого копирования
                    # rsync -av --delete ./ /mnt/c/dev/a_data/
                '''
            }
        }
        
        // Ваши другие стадии (например, 'Run Script on WSL') могут быть здесь
        // stage('Run Script on WSL') {
        //    steps {
        //        ...
        //    }
        // }
    }
    
}
