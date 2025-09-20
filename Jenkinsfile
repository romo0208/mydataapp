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
    }
}
