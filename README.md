image-insert-into-database-
===========================


#!/usr/bin/python

import MySQLdb


def main():

    db = MySQLdb.connect("localhost","root","6Tresxcvbhy", "zivame")
    cursor = db.cursor()

    sql = "SELECT * FROM  zivame_images WHERE upload_image_status = 'NO' and  	failing_status = 'NO' "

    results = ''

    try:
        cursor.execute(sql)
        results = cursor.fetchall()
    except:
        pass 

    if results:
        db2 = MySQLdb.connect("54.201.218.138","root","india@123", "zov")
        cursor2 = db2.cursor()

        sql = """create table IF NOT EXISTS  zov_jai_images (
                 product_id varchar(50),
                 image_link text
                 )"""

        cursor2.execute(sql)


        sql = """alter ignore  table  zov_jai_images add upload_image_status enum('NO','YES', 'F') DEFAULT 'NO';"""

        try:
            cursor2.execute(sql)
            db2.commit()
        except:
            pass

        sql = """alter ignore  table  zov_jai_images add failing_status  enum('NO','YES', 'F') DEFAULT 'NO';"""

        try:
            cursor2.execute(sql)
            db2.commit()
        except:
            pass

        for row in results:
            #print row
            sql = """INSERT INTO zov_jai_images(product_id, image_link)
                     VALUES ("%s", "%s")"""
            img = row[2]
            #start = img.rfind("?")
            #end = img.rfind("/")
            #img = img[end+1:start]
            img = img.split("/")[-1]
            sql = sql %(str(row[1]), img)
           # print row[0]

            try:
                cursor2.execute(sql)
                db2.commit()
                print "inserted...................................."
            except:
                db2.rollback()

        db2.close() 

    sql = """UPDATE  zivame_images  set upload_image_status = 'YES' """
    cursor.execute(sql)
    db.commit()

    db.close()





def supermain():
    main()



if __name__=="__main__":
    supermain()
