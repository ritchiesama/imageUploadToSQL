```java
/*
 * To change this license header, choose License Headers in Project Properties.
 * To change this template file, choose Tools | Templates
 * and open the template in the editor.
 */
package uploadtest;
 
public class Main
{
    
    /**
     * main:(test upload). <br/>
     * @author Ritchie Li
     * @param args
     * @since JDK 1.6
     */
    public static void main(String[] args)
    {
        long startTime = System.currentTimeMillis();
        System.out.println("--------------------------start--------------------------------------");
        UpLoadPhoto.insertPhoto("D:\\folder\\folder");
        long endTime = System.currentTimeMillis();
        System.out.println("--------------------------upload time:" + (endTime - startTime) / 1000 + " sec--------------------------");
        
    }
}


/*
 * To change this license header, choose License Headers in Project Properties.
 * To change this template file, choose Tools | Templates
 * and open the template in the editor.
 */

package uploadtest;
 
import java.sql.*;

 
public class GetConnection
{
    /**
     * getConnection:(connect to database). 
     * @author Ritchie Li
     * @return
     * @since JDK 1.6
     */
    public static Connection getConnection()
    {

        String url = "jdbc:sqlserver://xxx.xxx.xxx.com;databaseName=TypeDataBaseNameIfNeeded";
        String username = "Username";
        String password = "Password";
        Connection connection = null;
        try{
             connection = DriverManager.getConnection(url,username,password);
            System.out.println("Connected to Microsoft SQL Server");
        }catch(SQLException e){
            System.out.println("error");
        }
        return connection;
    }
}




/*
 * To change this license header, choose License Headers in Project Properties.
 * To change this template file, choose Tools | Templates
 * and open the template in the editor.
 */
package uploadtest;
 
import java.io.BufferedInputStream;
import java.io.File;
import java.io.FileInputStream;
import java.sql.*;

 
public class UpLoadPhoto
{
    /**
     * UpLoad:(upload to database). <br/>
     * @author Ritchie Li
     * @param path file path
     * @since JDK 1.6
     */

//    public static void UpLoad(String path)
//    {
//        Connection connection = GetConnection.getConnection();
//        insertPhoto(path, connection);
//    }
//    
    /**
     * insertPhoto:(insert image). <br/>
     * @author Ritchie Li
     * @param path image folder 
     * @param connection connect object
     * @since JDK 1.6
     */
    //static PreparedStatement pstmt="";
    public static void insertPhoto(String path)
    {
        Connection connection = GetConnection.getConnection();
        File file = new File(path);   //get all the files in the path
        String[] photoName = file.list();   
        PreparedStatement pstmt = null;
        int num = 0;
        try
        {
            String sql ="insert into dbo.tableName (value1,value2,) values(?,?)"; // use values(?,?,?,?) to pretect SQL injection attact.
            pstmt = connection.prepareStatement(sql);
            System.out.println("pstmt="+ pstmt);
            String trackingid = "";
            String logoimage = "";
            for (int i = 0; i < photoName.length; i++)
            {
                logoimage = photoName[i];
                //get image name without image type
                trackingid = getFileName(logoimage);
                //get file 
                File file2 = new File(path + "\\" + logoimage);
                //read file
                BufferedInputStream bis = new BufferedInputStream(new FileInputStream(file2));
                //set primary key
                int trackingidInt = Integer.valueOf(trackingid).intValue();
                pstmt.setInt(1,trackingidInt);
 //               //get file name
 //               pstmt.setString(2, logoimage); 
//                //set source
//                pstmt.setString(3, "1");
//                //file name + file type
//                pstmt.setString(4, logoimage);
//                //size
//                pstmt.setLong(5, file2.length());
                //set image content 
                pstmt.setBinaryStream(2, bis, (int)file2.length());
                System.out.println("success!");
                num += pstmt.executeUpdate();
            }
        }
        catch (Exception e)
        {
            e.printStackTrace();
        }
        finally
        {
            try
            {
                if (pstmt != null)
                {
                    pstmt.close();
                }
                if (connection != null)
                {
                    connection.close();
                }
                
            }
            catch (SQLException e)
            {
                e.printStackTrace();
                
            }
        }
        
        System.out.println(" Successd to upload " + num + " image files");
    }
    
    /**
     * getFileName:(get files name). 
     * @author Ritchie Li
     * @param logoimage
     * @return file name
     * @since JDK 1.6
     */
    public static String getFileName(String pn)
    {
        String fileName = "";
        if (pn.contains("（"))
        {
            fileName = pn.split("（")[0];
        }
        //e.g 123.jpg  then return 123
        else if (pn.contains(".jpg"))
        {
            fileName = pn.split(".jpg")[0];
        }
        //e.g 123.JPG  then return 123
        else if (pn.contains(".JPG"))
        {
            fileName = pn.split(".JPG")[0];
        }
        else if (pn.contains(".png"))
        {
            fileName = pn.split(".png")[0];
        }
        //e.g 123.JPG  then return 123
        else if (pn.contains(".PNG"))
        {
            fileName = pn.split(".PNG")[0];
        }
        else
        {
            fileName = pn;
        }
        
        return fileName;
    }
}
```
