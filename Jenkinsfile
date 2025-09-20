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
                // Используем fileOperations для копирования всего содержимого workspace
                fileOperations([
                    folderCopyOperation(
                        sourceFolderPath: '.',       // Копируем из текущего workspace Jenkins
                        destinationFolderPath: '/mnt/c/dev/a_data' // Целевая директория
                        // excludes: '**/*.log',     // Опционально: исключаем ненужные файлы по паттерну
                        // flattenFiles: false       // Опционально: сохраняем структуру папок
                    )
                ])
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
