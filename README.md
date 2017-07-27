# hello-world
Hello world demo 
//
//  StudentInfoViewController.m
//  Student Planner
//
//  Created by necixy on 25/07/17.
//  Copyright © 2017 necixy. All rights reserved.
//

#import "StudentInfoViewController.h"
#import "studentsInfo.h"

@interface StudentInfoViewController ()
{
    NSArray *sectionName;
    NSMutableArray *sectionRows;
    NSArray *sortedStudentsArray;
    NSMutableArray *topThreeStudents;
    NSMutableArray *restStudents;
    
    
}

@end

@implementation StudentInfoViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    
    self.studentsinfoTableview.allowsMultipleSelectionDuringEditing = NO;
    

    
    self.allStudentsData = [[[NSUserDefaults standardUserDefaults] valueForKey:@"allStudentsInfo_key"] mutableCopy];
   // NSLog(@"All Stusents data: %@", self.allStudentsData);
    
    
    // sort all student in Decending order
    NSSortDescriptor *sortByName = [NSSortDescriptor sortDescriptorWithKey:@"marks_key"
                                                                 ascending:NO];
    NSArray *sortDescriptors = [NSArray arrayWithObject:sortByName];
    sortedStudentsArray = [ self.allStudentsData sortedArrayUsingDescriptors:sortDescriptors];
   // NSLog(@"sorted data: %@", sortedStudentsArray);
    
    
    topThreeStudents = [[NSMutableArray alloc]init];
    for (int i=0; i<3; i++)
    {
        NSArray *temp = [[sortedStudentsArray objectAtIndex:i] mutableCopy];
        [topThreeStudents addObject:temp];
    }
   // NSLog(@"top three %@",topThreeStudents);
    
    
    restStudents = [[NSMutableArray alloc]init];
    for (int i=3; i<self.allStudentsData.count; i++)
    {
        NSArray *temp = [[sortedStudentsArray objectAtIndex:i] mutableCopy];
        [restStudents addObject:temp];
    }
    //NSLog(@"rest all %@",restStudents);
    
    sectionName = [[NSArray alloc] initWithObjects:@"Highest score", @"Rest students", nil];
    // NSLog(@"Section Name: %@", sectionName);
    
    sectionRows = [[NSMutableArray alloc]initWithObjects:[NSNumber numberWithInteger:3], [NSNumber numberWithInteger:self.allStudentsData.count - 3], nil];
    
    
//        if (self.allStudentsData.count == 1) {
//    
//            sectionRows = [[NSMutableArray alloc]initWithObjects:[NSNumber numberWithInteger:1], [NSNumber numberWithInteger:0], nil];
//        }
//        else if (self.allStudentsData.count == 2)
//        {
//            sectionRows = [[NSMutableArray alloc]initWithObjects:[NSNumber numberWithInteger:2], [NSNumber numberWithInteger:0], nil];
//    
//        }
//        else if (self.allStudentsData.count == 3)
//        {
//            sectionRows = [[NSMutableArray alloc]initWithObjects:[NSNumber numberWithInteger:3], [NSNumber numberWithInteger:0], nil];
//    
//        }
//        else if (self.allStudentsData.count == 3)
//        {
//    
//            sectionRows = [[NSMutableArray alloc]initWithObjects:[NSNumber numberWithInteger:3], [NSNumber numberWithInteger:self.allStudentsData.count - 3], nil];
//        }
    
}


-(UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath
{
    studentsInfo *cell;
    static NSString *simpleTableIdentifier = @"SimpleTableItem";
    
    cell = [tableView dequeueReusableCellWithIdentifier:simpleTableIdentifier];
    
    cell.selectionStyle = UITableViewCellSelectionStyleNone;
    if (cell == nil)
    {
        [tableView registerNib:[UINib nibWithNibName:@"studentsInfo" bundle:nil] forCellReuseIdentifier:@"SimpleTableItem"];
        
        cell = [tableView dequeueReusableCellWithIdentifier:@"SimpleTableItem"];
    }
    return cell;
    
}



-(void)tableView:(UITableView *)tableView willDisplayCell:(studentsInfo *)cell forRowAtIndexPath:(NSIndexPath *)indexPath
{
    
    if (indexPath.section == 0)
    {
        NSMutableDictionary *dict = (NSMutableDictionary*)[topThreeStudents objectAtIndex:indexPath.row];
        NSString *name = [dict valueForKey:@"name_key"];
        cell.cellNameLabel.text = name;
        
        NSNumber *marks = [dict valueForKey:@"marks_key"];
        NSString *marksInString = [NSString stringWithFormat:@"%@",marks];
        cell.cellMarkslabel.text = marksInString;
        
        NSString *folderPath = [self pathToPatientPhotoFolder];
        NSString *profilePicName = [dict valueForKey:@"profile_pic__key"];
        NSString *savedImageName= [NSString stringWithFormat:@"%@/%@",folderPath,profilePicName];
       // NSLog(@"path %@",savedImageName);
        
        UIImage *image = [UIImage imageWithContentsOfFile:savedImageName];
        cell.cellImageView.image =image;
        
        
        
    }
    else
    {
        NSMutableDictionary *dict = (NSMutableDictionary*)[restStudents objectAtIndex:indexPath.row];
        NSString *name = [dict valueForKey:@"name_key"];
        cell.cellNameLabel.text = name;
        
        NSNumber *marks = [dict valueForKey:@"marks_key"];
        NSString *marksInString = [NSString stringWithFormat:@"%@",marks];
        cell.cellMarkslabel.text = marksInString;
        
        
        NSString *folderPath = [self pathToPatientPhotoFolder];
        NSString *profilePicName = [dict valueForKey:@"profile_pic__key"];
        NSString *savedImageName= [NSString stringWithFormat:@"%@/%@",folderPath,profilePicName];
        //NSLog(@"path %@",savedImageName);
        
        UIImage *image = [UIImage imageWithContentsOfFile:savedImageName];
        cell.cellImageView.image =image;
    }
    
    
}

- (NSInteger)numberOfSectionsInTableView:(UITableView *)tableView
{
    // Return the number of sections.
    return 2.0f;
}

- (NSString *)tableView:(UITableView *)tableView titleForHeaderInSection:(NSInteger)section
{
    // Return the title of sections.
    return [sectionName objectAtIndex:section];
}

-(NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section
{
    return [[sectionRows objectAtIndex:section] intValue];
    
}

-(CGFloat)tableView:(UITableView *)tableView heightForRowAtIndexPath:(NSIndexPath *)indexPath
{
    return 68.0f;
}

- (void)tableView:(UITableView *)tableView willDisplayHeaderView:(UIView *)view forSection:(NSInteger)section
{
    // Text Color
    UITableViewHeaderFooterView *header = (UITableViewHeaderFooterView *)view;
    [header.textLabel setTextColor:[UIColor colorWithRed:51.0/255.0 green:92.0/255.0 blue:255.0/255.0 alpha:1.0]];
    
    
    // Another way to set the background color
    // Note: does not preserve gradient effect of original header
    header.contentView.backgroundColor = [UIColor colorWithRed:245.0/255.0 green:245.0/255.0 blue:245.0/255.0 alpha:1.0];
}
-(void) tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath event:(id)event
{
    
    
}

- (BOOL)tableView:(UITableView *)tableView canEditRowAtIndexPath:(NSIndexPath *)indexPath {
    // Return YES if you want the specified item to be editable.
    return YES;
}

// Override to support editing the table view.
- (void)tableView:(UITableView *)tableView commitEditingStyle:(UITableViewCellEditingStyle)editingStyle forRowAtIndexPath:(NSIndexPath *)indexPath
{
    
    if (editingStyle == UITableViewCellEditingStyleDelete) {
        [self.allStudentsData removeObjectAtIndex:indexPath.row];
        [tableView deleteRowsAtIndexPaths:@[indexPath] withRowAnimation:UITableViewRowAnimationAutomatic];
    
    }
    
//    if (editingStyle == UITableViewCellEditingStyleDelete)
//    {
//        //add code here for when you hit delete
//        
//        [sortedStudentsArray removeObjectAtIndex:indexPath.row];
//             NSLog(@"sorted data after delete: %@", sortedStudentsArray);
//        [self.studentsinfoTableview deleteRowsAtIndexPaths:[NSArray arrayWithObject:indexPath] withRowAnimation:UITableViewRowAnimationLeft];
//        
//        [self.studentsinfoTableview reloadData];
//   
//    }
}


- (void)removeImage:(NSString *)filename
{
    NSFileManager *fileManager = [NSFileManager defaultManager];
    NSString *documentsPath = [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) objectAtIndex:0];
    
    NSString *filePath = [documentsPath stringByAppendingPathComponent:filename];
    NSError *error;
    BOOL success = [fileManager removeItemAtPath:filePath error:&error];
    if (success) {
        UIAlertView *removedSuccessFullyAlert = [[UIAlertView alloc] initWithTitle:@"Congratulations:" message:@"Successfully removed" delegate:self cancelButtonTitle:@"Close" otherButtonTitles:nil];
        [removedSuccessFullyAlert show];
    }
    else
    {
        NSLog(@"Could not delete file -:%@ ",[error localizedDescription]);
    }
}

- (NSString *)pathToPatientPhotoFolder
{
    
    //Create a new folder for Images
    NSString *documentsDirectory = [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory,
                                                                        NSUserDomainMask,
                                                                        YES) lastObject];
    NSString *patientPhotoFolder = [documentsDirectory stringByAppendingPathComponent:@"PhotoFolder"];
    
    // Create the folder if necessary
    BOOL isDir = NO;
    NSFileManager *fileManager = [[NSFileManager alloc] init];
    if (![fileManager fileExistsAtPath:patientPhotoFolder
                           isDirectory:&isDir] && isDir == NO) {
        [fileManager createDirectoryAtPath:patientPhotoFolder
               withIntermediateDirectories:NO
                                attributes:nil
                                     error:nil];
    }
    return patientPhotoFolder;
}
- (void)didReceiveMemoryWarning {
    [super didReceiveMemoryWarning];
    // Dispose of any resources that can be recreated.
}

@end
